# Authentication Mechanisms trong RESTful APIs

## 1. Basic Authentication

### Cơ chế hoạt động
```
Authorization: Basic base64(username:password)
```

- Client gửi username và password được mã hóa base64
- Đơn giản, dễ implement
- Thường được dùng với HTTPS

### Ví dụ Implementation
```python
from base64 import b64encode

def create_basic_auth_header(username, password):
    credentials = f"{username}:{password}"
    encoded = b64encode(credentials.encode()).decode()
    return f"Basic {encoded}"

# Usage
headers = {
    'Authorization': create_basic_auth_header('user', 'pass')
}
```

### Ưu/Nhược điểm
✅ Đơn giản, dễ implement  
✅ Được hỗ trợ rộng rãi  
❌ Gửi credentials trong mỗi request  
❌ Không có expiration  
❌ Không an toàn nếu không dùng HTTPS

## 2. JWT (JSON Web Tokens)

### Cơ chế hoạt động
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

- Token bao gồm 3 phần: Header, Payload, Signature
- Stateless authentication
- Thông tin được mã hóa trong token

### Ví dụ Implementation
```python
import jwt

def create_jwt(payload, secret_key):
    return jwt.encode(payload, secret_key, algorithm='HS256')

def verify_jwt(token, secret_key):
    try:
        return jwt.decode(token, secret_key, algorithms=['HS256'])
    except jwt.InvalidTokenError:
        return None

# Usage
token = create_jwt({'user_id': 123}, 'secret')
headers = {'Authorization': f'Bearer {token}'}
```

### Ưu/Nhược điểm
✅ Stateless  
✅ Có thể chứa thông tin trong payload  
✅ Dễ scale  
❌ Token size lớn  
❌ Không thể thu hồi token (cần blacklist)

## 3. OAuth 2.0

### Các Grant Types
1. **Authorization Code**
   - Dùng cho web apps
   - Secure nhất
   - Có refresh token

2. **Password Grant**
   - Dùng cho trusted clients
   - Client lấy trực tiếp username/password

3. **Client Credentials**
   - Dùng cho service-to-service
   - Không có user interaction

### Flow Authorization Code
```mermaid
sequenceDiagram
    Client->>Auth Server: Authorization Request
    Auth Server->>User: Login & Consent
    User->>Auth Server: Approve
    Auth Server->>Client: Authorization Code
    Client->>Auth Server: Code + Client Credentials
    Auth Server->>Client: Access Token + Refresh Token
```

### Ví dụ Implementation
```python
from oauthlib.oauth2 import WebApplicationClient

client = WebApplicationClient(client_id)

# Authorization Request
auth_url = client.prepare_request_uri(
    'https://auth-server/auth',
    redirect_uri='https://client/callback',
    scope=['profile', 'email']
)

# Token Request
token_url = 'https://auth-server/token'
token_response = requests.post(
    token_url,
    data=client.prepare_token_request(
        token_url,
        authorization_response=callback_url,
        redirect_url='https://client/callback'
    )
)
```

## 4. API Keys

### Cơ chế hoạt động
```
Authorization: ApiKey your-api-key-here
# hoặc
X-API-Key: your-api-key-here
```

- Key unique cho mỗi client
- Thường dùng cho public APIs
- Có thể rate limit theo key

### Implementation Example
```python
def verify_api_key(request):
    api_key = request.headers.get('X-API-Key')
    return database.verify_key(api_key)

# Usage
headers = {'X-API-Key': 'your-api-key'}
```

## 5. Session-based Authentication

### Cơ chế hoạt động
```
Cookie: session_id=abc123
```

- Server lưu session trong database/cache
- Client lưu session ID trong cookie
- Stateful authentication

### Ví dụ Implementation
```python
from flask import session
import redis

redis_client = redis.Redis()

def login_user(user_id):
    session_id = generate_session_id()
    redis_client.setex(f"session:{session_id}", 
                      3600,  # 1 hour
                      user_id)
    return session_id

def verify_session(session_id):
    return redis_client.get(f"session:{session_id}")
```

## 6. Security Best Practices

### 1. HTTPS
- Luôn sử dụng HTTPS
- Implement HSTS
- Secure cookies

### 2. Rate Limiting
```python
from flask_limiter import Limiter

limiter = Limiter(
    key_func=get_remote_address,
    default_limits=["200 per day", "50 per hour"]
)
```

### 3. Password Hashing
```python
from passlib.hash import pbkdf2_sha256

def hash_password(password):
    return pbkdf2_sha256.hash(password)

def verify_password(password, hash):
    return pbkdf2_sha256.verify(password, hash)
```

### 4. Token Security
- Short expiration time
- Secure storage
- Rotation policy
- Implement refresh tokens

## 7. Multi-factor Authentication (MFA)

### Implementation với TOTP
```python
import pyotp

def generate_totp_secret():
    return pyotp.random_base32()

def verify_totp(secret, token):
    totp = pyotp.TOTP(secret)
    return totp.verify(token)

# Usage
secret = generate_totp_secret()
is_valid = verify_totp(secret, "123456")
```

## 8. Hybrid Authentication

Kết hợp nhiều phương pháp:
1. JWT + API Key
2. OAuth + MFA
3. Session + JWT

### Ví dụ JWT + API Key
```python
def authenticate(request):
    # Verify API Key
    api_key = request.headers.get('X-API-Key')
    if not verify_api_key(api_key):
        return False
        
    # Verify JWT
    token = request.headers.get('Authorization')
    if not verify_jwt(token):
        return False
        
    return True
```
