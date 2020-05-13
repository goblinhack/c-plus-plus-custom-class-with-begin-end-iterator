Example implementation of a vector continer with begin() and end()
==================================================================

Expected output:
```
 c: walk 10
 c: walk 11
```

Compile as:
```
  g++ -std=c++2a -O2 -Wall -pedantic foo.cpp
```

Code:
```C++
#include <iostream>
#include <algorithm>

template<class T> class MyVector {
private:
    T *data;
    size_t maxlen;
    size_t len;
public:
    MyVector<T> () : data (nullptr), maxlen(0), len(0) { }
    MyVector<T> (int maxlen) : data (new T [maxlen]), maxlen(maxlen), len(0) { }

    MyVector<T> (const MyVector& o) {
        std::cout << "copy ctor called" << std::endl;
        data = new T [o.maxlen];
        maxlen = o.maxlen;
        len = o.len;
        std::copy(o.data, o.data + o.maxlen, data);
    }

    MyVector<T> (const MyVector<T>&& o) {
        std::cout << "move ctor called" << std::endl;
        data = o.data;
        maxlen = o.maxlen;
        len = o.len;
    }

    void push_back (const T& i) {
        if (len >= maxlen) {
            maxlen *= 2;
            auto newdata = new T [maxlen];
            std::copy(data, data + len, newdata);
            if (data) {
                delete[] data;
            }
            data = newdata;
        }
        data[len++] = i;
    }

    friend std::ostream& operator<<(std::ostream &os, const MyVector<T>& o) {
        auto s = o.data;
        auto e = o.data + o.len;
        while (s < e) {
            os << "[" << *s << "]";
            s++;
        }
        return os;
    }

    class Iterator {
    public:
        Iterator(T* ptr) : ptr(ptr) {}
        Iterator operator++() { ++ptr; return *this; }
        bool operator!= (const Iterator& o) const {
            return ptr != o.ptr;
        }
        const T& operator*() const { return *ptr; }
   private:
       T* ptr;
   };
public:
   Iterator begin() const { return Iterator(data); }
   Iterator end() const { return Iterator(data + len); }
};

int main() {
    auto c = MyVector<int>(1);
    c.push_back(10);
    c.push_back(11);
    for (auto i : c) {
        std::cout << "c: walk " << i << std::endl;
    }
}
```
