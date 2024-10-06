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
// Для избежания конфликов с std
namespace MyNamespace {

    class Vector {
    private:
        int* data;
        size_t _size;     
        size_t _capacity; 

    public:
        Vector() : data(nullptr), _size(0), _capacity(0) {}

        size_t size() const {
            return _size; 
        }

        size_t capacity() const {
            return _capacity; 
        }

        void clear() {
            delete[] data;
            data = nullptr; 
            _size = 0;
            _capacity = 0;
        };

        void reserve(size_t n) {
            if (n <= _capacity) return;

            // Выделяем новый кусок памяти и перезаписываем в него значения из текущего
            int* newData = new int[n];
            for (size_t i = 0; i < _size; ++i) {
                newData[i] = data[i];
            }
            delete[] data;
            data = newData;
            _capacity = n;
        };

        void resize(size_t p_size, int value) {
            // если переданная p_size меньше -> просто обрезаем до указанной длины
            if (p_size < _size) {
                _size = p_size; // теперь индексы за пределами недоступны
            }
            else if (p_size == _size) {
                return;
            }
            else {
                reserve(p_size); // выделяем новый кусок памяти с перезаписью текущих значений

                // в цикле после предыдущих добавляем новые переданные value
                for (size_t i = _size; i < p_size; ++i) {
                    data[i] = value;
                }

                _size = p_size;
            }
        };

        void shrink_to_fit() {
            if (_capacity == _size) return;

            int* newData = new int[_size];
            for (size_t i = 0; i < _size; ++i) {
                newData[i] = data[i];
            }

            delete[] data;
            data = newData;
            _capacity = _size;
        };

        void push_back(const int elem) {
            if (_capacity == _size) {
                reserve(_size ? _size * 2 : 1); // если вектор пуст, reserve выделит память под один эл-т
            }

            data[_size] = elem;
            ++_size;
        };

        void pop_back() {
            if (_size == 0) return;

            --_size; // просто уменьшаем размер

            // Для освобождения памяти можно вызвать shrink_to_fit()
            // Но это необязательно, т.к. выделенную память займут новые элементы
        };

        int at(size_t index) const {
            if (index >= _size) {
                throw std::out_of_range("Index out of range");
            }
            return data[index];
        }
    };
}

int main() {
    MyNamespace::Vector myVec;

    // Тестирование push_back
    myVec.push_back(10);
    myVec.push_back(20);
    myVec.push_back(30);

    // Тестирование размера и емкости
    std::cout << "MyNamespace::Vector size: " << myVec.size() << std::endl; // Ожидается 3
    std::cout << "MyNamespace::Vector capacity: " << myVec.capacity() << std::endl; // Ожидается 4 или больше

    // Тестирование доступа к элементам
    for (size_t i = 0; i < myVec.size(); ++i) {
        std::cout << "Element at index " << i << ": " << myVec.at(i) << std::endl;
    }

    // Тестирование pop_back
    myVec.pop_back();
    std::cout << "After pop_back, size: " << myVec.size() << std::endl; // Ожидается 2

    // Тестирование clear
    myVec.clear();
    std::cout << "After clear, size: " << myVec.size() << std::endl; // Ожидается 0
    std::cout << "After clear, capacity: " << myVec.capacity() << std::endl; // Ожидается 0

    return 0;
}
```
