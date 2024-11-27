# Cấu Trúc Dữ Liệu Nâng Cao Trong Python

## 1. Collections.deque

### Ưu điểm
- Thao tác thêm/xóa ở cả hai đầu với độ phức tạp O(1)
- Thread-safe, có thể dùng trong môi trường đa luồng
- Hỗ trợ giới hạn kích thước (maxlen)
- Hiệu quả hơn list khi cần thao tác ở đầu mảng

### Nhược điểm
- Truy cập ngẫu nhiên chậm hơn list O(n)
- Chiếm nhiều bộ nhớ hơn list
- Không hỗ trợ slicing như list

### Use Cases
```python
from collections import deque

# Queue trong xử lý tác vụ
task_queue = deque(maxlen=1000)
task_queue.append("new_task")
current_task = task_queue.popleft()

# Lưu trữ lịch sử thao tác
history = deque(maxlen=10)
history.append("action1")
last_action = history.pop()

# Buffer trong xử lý stream data
buffer = deque(maxlen=256)
```

## 2. Set và Frozenset

### Ưu điểm
- Tìm kiếm, thêm, xóa với độ phức tạp O(1)
- Loại bỏ tự động các phần tử trùng lặp
- Hỗ trợ các phép toán tập hợp (union, intersection)
- Frozenset có thể dùng làm key trong dictionary

### Nhược điểm
- Không duy trì thứ tự phần tử
- Chỉ lưu trữ được hashable objects
- Chiếm nhiều bộ nhớ hơn list
- Không hỗ trợ indexing

### Use Cases
```python
# Loại bỏ trùng lặp
unique_items = set([1, 2, 2, 3, 3, 3])

# Tìm phần tử chung
set1 = {1, 2, 3}
set2 = {3, 4, 5}
common = set1 & set2

# Cache với frozenset
cache = {
    frozenset([1, 2]): "result1",
    frozenset([3, 4]): "result2"
}
```

## 3. Collections.Counter

### Ưu điểm
- Tự động đếm số lần xuất hiện
- Hỗ trợ các phép toán số học (+, -)
- Có các method hữu ích như most_common()
- Kế thừa từ dict nên có các phương thức của dict

### Nhược điểm
- Chiếm nhiều bộ nhớ hơn dict thông thường
- Không phù hợp với dữ liệu không đếm được
- Không duy trì thứ tự đếm

### Use Cases
```python
from collections import Counter

# Phân tích từ trong văn bản
text = "hello world hello"
word_count = Counter(text.split())

# Tìm phần tử phổ biến nhất
data = [1, 1, 1, 2, 2, 3]
counter = Counter(data)
most_common = counter.most_common(1)

# So sánh tần suất
counter1 = Counter(['a', 'b', 'b'])
counter2 = Counter(['b', 'b', 'c'])
difference = counter1 - counter2
```

## 4. Heapq (Priority Queue)

### Ưu điểm
- Tự động duy trì thứ tự ưu tiên
- Thao tác thêm/xóa với độ phức tạp O(log n)
- Tiết kiệm bộ nhớ hơn so với các giải pháp khác
- Có thể dùng cho cả min-heap và max-heap

### Nhược điểm
- Không hỗ trợ tìm kiếm trực tiếp
- Không thread-safe
- Cần viết thêm code để xử lý max-heap
- Khó debug vì cấu trúc heap không trực quan

### Use Cases
```python
import heapq

# Lập lịch task theo độ ưu tiên
tasks = [(4, 'low priority'), (2, 'high priority'), (3, 'medium priority')]
heapq.heapify(tasks)
next_task = heapq.heappop(tasks)

# Top K elements
numbers = [10, 4, 5, 8, 6, 11, 26]
k = 3
largest = heapq.nlargest(k, numbers)

# Merge sorted iterables
list1 = [1, 3, 5]
list2 = [2, 4, 6]
merged = list(heapq.merge(list1, list2))
```

## 5. Collections.defaultdict

### Ưu điểm
- Tự động khởi tạo giá trị mặc định cho key mới
- Code ngắn gọn hơn, ít phải kiểm tra
- Linh hoạt với nhiều kiểu dữ liệu mặc định
- Kế thừa từ dict nên có đầy đủ tính năng của dict

### Nhược điểm
- Có thể gây khó debug nếu không hiểu rõ cơ chế
- Chiếm nhiều bộ nhớ hơn dict thông thường
- Có thể tạo key không mong muốn nếu không cẩn thận

### Use Cases
```python
from collections import defaultdict

# Grouping
groups = defaultdict(list)
for item in ['A1', 'A2', 'B1', 'B2']:
    groups[item[0]].append(item)

# Counting with default value
count = defaultdict(int)
for char in "hello world":
    count[char] += 1

# Nested dictionaries
nested = defaultdict(lambda: defaultdict(list))
nested['level1']['level2'].append('value')
```

## 6. Array

### Ưu điểm
- Tiết kiệm bộ nhớ hơn list
- Hiệu suất cao hơn khi làm việc với số
- Type safety, đảm bảo kiểu dữ liệu đồng nhất
- Tích hợp tốt với C/C++

### Nhược điểm
- Chỉ lưu trữ được một kiểu dữ liệu
- Ít linh hoạt hơn list
- Không hỗ trợ một số phương thức của list
- Khó thao tác với dữ liệu phức tạp

### Use Cases
```python
from array import array

# Xử lý dữ liệu số học
numbers = array('i', [1, 2, 3, 4, 5])
numbers.append(6)

# Đọc/ghi dữ liệu nhị phân
with open('data.bin', 'wb') as f:
    numbers.tofile(f)

# Tính toán số học hiệu năng cao
float_array = array('d', [1.0, 2.0, 3.0])
result = sum(float_array)
```

## Tips Chọn Cấu Trúc Dữ Liệu

1. Chọn deque khi:
   - Cần thao tác thêm/xóa ở cả hai đầu
   - Làm việc với queue/stack
   - Cần giới hạn kích thước buffer

2. Chọn set khi:
   - Cần kiểm tra tồn tại nhanh
   - Loại bỏ trùng lặp
   - Thực hiện các phép toán tập hợp

3. Chọn Counter khi:
   - Cần đếm số lần xuất hiện
   - Tìm phần tử phổ biến nhất
   - So sánh tần suất

4. Chọn heapq khi:
   - Cần queue có ưu tiên
   - Tìm top K phần tử
   - Merge các danh sách đã sắp xếp

5. Chọn defaultdict khi:
   - Cần grouping/counting
   - Xử lý cấu trúc dữ liệu lồng nhau
   - Muốn code ngắn gọn hơn

6. Chọn array khi:
   - Làm việc với số học thuần túy
   - Cần tiết kiệm bộ nhớ
   - Tích hợp với C/C++
