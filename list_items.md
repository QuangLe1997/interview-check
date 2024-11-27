# List, Iterator và Generator trong Python

## 1. List

### Đặc điểm của List
- Là cấu trúc dữ liệu có thứ tự và có thể thay đổi (mutable)
- Có thể chứa nhiều kiểu dữ liệu khác nhau
- Hỗ trợ slicing và indexing
- Được cài đặt như một mảng động

### Các Phương Thức Quan Trọng
```python
# Khởi tạo
my_list = [1, 2, 3]
my_list = list()

# Thêm phần tử
my_list.append(4)        # Thêm vào cuối
my_list.insert(0, 0)     # Thêm vào vị trí chỉ định
my_list.extend([5, 6])   # Nối list

# Xóa phần tử
my_list.remove(1)        # Xóa giá trị đầu tiên tìm thấy
del my_list[0]           # Xóa theo index
popped = my_list.pop()   # Lấy và xóa phần tử cuối
my_list.clear()          # Xóa tất cả

# Tìm kiếm và sắp xếp
index = my_list.index(3)  # Tìm vị trí
count = my_list.count(2)  # Đếm số lần xuất hiện
my_list.sort()           # Sắp xếp tại chỗ
my_list.reverse()        # Đảo ngược list
```

### Slicing và List Comprehension
```python
# Slicing
lst = [0, 1, 2, 3, 4, 5]
sub_list = lst[1:4]      # [1, 2, 3]
reversed_list = lst[::-1] # [5, 4, 3, 2, 1, 0]

# List Comprehension
squares = [x**2 for x in range(10)]
evens = [x for x in range(10) if x % 2 == 0]
matrix = [[i+j for j in range(3)] for i in range(3)]
```

## 2. Iterator

### Khái Niệm
- Iterator là đối tượng hỗ trợ phương thức `__iter__()` và `__next__()`
- Dùng để duyệt qua các phần tử của collection
- Tiết kiệm bộ nhớ vì không cần lưu toàn bộ dữ liệu
- Raise StopIteration khi hết phần tử

### Tạo Iterator Tùy Chỉnh
```python
class CountUpTo:
    def __init__(self, max_value):
        self.max_value = max_value
        self.current = 0
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.current < self.max_value:
            self.current += 1
            return self.current
        raise StopIteration

# Sử dụng
counter = CountUpTo(3)
for num in counter:
    print(num)  # In ra: 1, 2, 3
```

### Built-in Iterators
```python
# iter() function
lst = [1, 2, 3]
iterator = iter(lst)
print(next(iterator))  # 1
print(next(iterator))  # 2

# enumerate
for i, value in enumerate(['a', 'b', 'c']):
    print(f"{i}: {value}")

# zip
for x, y in zip([1, 2, 3], ['a', 'b', 'c']):
    print(f"{x}-{y}")
```

## 3. Generator và Yield

### Khái Niệm
- Generator là một loại iterator đặc biệt
- Được tạo bằng từ khóa `yield`
- Lưu trạng thái của hàm và tiếp tục từ đó
- Tiết kiệm bộ nhớ với dữ liệu lớn

### Generator Function
```python
def count_up_to(max_value):
    current = 0
    while current < max_value:
        current += 1
        yield current

# Sử dụng
for num in count_up_to(3):
    print(num)  # In ra: 1, 2, 3

# Generator expression
squares = (x**2 for x in range(1000000))  # Tiết kiệm bộ nhớ hơn list comprehension
```

### Ví Dụ Thực Tế
```python
def read_large_file(file_path):
    with open(file_path) as f:
        while True:
            line = f.readline()
            if not line:
                break
            yield line.strip()

# Đọc file lớn hiệu quả
for line in read_large_file('large_file.txt'):
    process_line(line)

# Generator cho Fibonacci
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# Lấy 10 số Fibonacci đầu tiên
fib = fibonacci()
for _ in range(10):
    print(next(fib))
```

### So Sánh List vs Generator
```python
# List - tạo và lưu trữ tất cả giá trị
numbers_list = [x for x in range(1000000)]  # Sử dụng nhiều bộ nhớ

# Generator - tạo giá trị khi cần
numbers_gen = (x for x in range(1000000))   # Sử dụng ít bộ nhớ

# Memory usage
import sys
print(sys.getsizeof(numbers_list))  # Lớn
print(sys.getsizeof(numbers_gen))   # Nhỏ
```

## Khi Nào Sử Dụng?

### Sử Dụng List khi:
- Cần truy cập ngẫu nhiên các phần tử
- Cần thay đổi dữ liệu
- Cần sử dụng lại dữ liệu nhiều lần
- Kích thước dữ liệu nhỏ và cố định

### Sử Dụng Iterator khi:
- Cần duyệt qua dữ liệu theo tuần tự
- Muốn tùy chỉnh cách duyệt
- Làm việc với collection tùy chỉnh

### Sử Dụng Generator khi:
- Xử lý dữ liệu lớn
- Đọc file lớn
- Tạo dữ liệu vô hạn
- Cần tiết kiệm bộ nhớ
