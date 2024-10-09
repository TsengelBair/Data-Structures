## Linked List

```c++
template <typename T>
class List {
private:
    struct Node {
        T* data; // указатель, а не фактические данные, т.к. данные м.б. тяжеловесными
        Node* next;

        Node(T* p_data) : data(p_data), next(nullptr) {};
    };

    Node* head;
    Node* tail;
    size_t size;

public:
    List() : head(nullptr), tail(nullptr), size(0) {}

    ~List() {
        while (!empty()) {
            pop_front();
        }
    }

    size_t getSize() const {
        return size;
    }

    bool empty() const {
        return size == 0;
    }

    void push_back(T* value) {
        Node* newTail = new Node(value);
        if (!head) {
            head = newTail;
            tail = newTail;
        }
        else {
            tail->next = newTail;
            tail = newTail;
        }
        size++;
    }

    void pop_back() {
        if (!head) return;

        if (head == tail) {
            delete head->data;
            delete head;
            head = nullptr;
            tail = nullptr;
        }
        else {
            Node* prevNode = head;
            while (prevNode->next != tail) {
                prevNode = prevNode->next;
            }

            delete tail->data;
            delete tail;

            tail = prevNode;
            tail->next = nullptr;
        }
        size--;
    }

    void push_front(T* value) {
        Node* newHead = new Node(value);
        if (!head) {
            head = newHead;
            tail = newHead;
        }
        else {
            newHead->next = head;
            head = newHead;
        }
        size++;
    }

    void pop_front() {
        if (!head) return;

        Node* oldHead = head;
        head = head->next;

        delete oldHead->data;
        delete oldHead;

        if (!head) {
            tail = nullptr; 
        }
        size--;
    }

    void insert(size_t index, T* value) {
        if (index > size) {
            cout << "Index out of bounds" << endl;
            return;
        }

        if (index == 0) {
            push_front(value);
        }
        else if (index == size) {
            push_back(value);
        }
        else {
            Node* newNode = new Node(value);
            Node* current = head;
            for (size_t i = 0; i < index - 1; ++i) {
                current = current->next;
            }

            newNode->next = current->next;
            current->next = newNode;
            size++;
        }
    }
};

```

## Vector

```c++
template <typename T>
class Vector {
private:
    T* data;
    size_t size;
    size_t capacity;

public:
    Vector() : data(nullptr), size(0), capacity(0) {}

    ~Vector() {
        clear();
    }

    size_t getSize() const {
        return size;
    }

    size_t getCapacity() const {
        return capacity;
    }

    void clear() {
        delete[] data;
        data = nullptr;
        size = 0;
        capacity = 0;
    }

    void reserve(size_t n) {
        if (n <= capacity) return;

        T* newData = new T[n]; 
        for (size_t i = 0; i < size; ++i) {
            newData[i] = data[i];
        }
        delete[] data;
        data = newData;
        capacity = n;
    }

    void resize(size_t p_size, const T& value = T()) { 
        if (p_size < size) {
            size = p_size;
        }
        else if (p_size == size) {
            return;
        }
        else {
            reserve(p_size);
            for (size_t i = size; i < p_size; ++i) {
                data[i] = value;
            }
            size = p_size;
        }
    }

    void shrink_to_fit() {
        if (capacity == size) return;

        T* newData = new T[size];
        for (size_t i = 0; i < size; ++i) {
            newData[i] = data[i];
        }
        delete[] data;
        data = newData;
        capacity = size;
    }

    void push_back(const T& elem) {
        if (capacity == size) {
            reserve(size ? size * 2 : 1);
        }
        data[size] = elem;
        ++size;
    }

    void pop_back() {
        if (size == 0) return;

        --size;
    }

    T at(size_t index) const { 
        if (index >= size) {
            throw std::out_of_range("Index out of range");
        }
        return data[index];
    }
};
}
```
