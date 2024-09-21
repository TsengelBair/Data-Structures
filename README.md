## Linked List

```c++
struct Node
{
    int val;
    Node* next;

    Node() : val(0), next(nullptr) {};
    Node(int x) : val(x), next(nullptr) {};
    Node(int x, Node* next) : val(x), next(next) {};
};

class LinkedList {
private:
    Node* head;
    Node* tail;
    int nodesCount;

public:
    LinkedList() : head(nullptr), tail(nullptr), nodesCount(0) {};

    ~LinkedList() {
        while (head != nullptr) {
            pop_front();
        }
    }

    int size() const {
        return nodesCount;
    }

    bool empty() {
        return nodesCount == 0;
    }

    void push_front(int x) {
        Node* newHead = new Node(x, head);
        head = newHead;
        if (nodesCount == 0) {
            tail = newHead; 
        }
        nodesCount++;
    }

    void pop_front() {
        if (head == nullptr) return; 

        Node* newHead = head->next;
        delete head;
        head = newHead;
        nodesCount--;

        if (nodesCount == 0) {
            tail = nullptr;
        }
    }

    void push_back(int x) {
        Node* newTail = new Node(x);

        if (nodesCount == 0) { 
            head = newTail; 
            tail = newTail;
        }
        else {
            tail->next = newTail;  // cтарый хвост указывает на новый элемент
            tail = newTail; 
        }

        nodesCount++;
    }


    void pop_back() {
        if (head == nullptr) return; 

        if (head == tail) { 
            delete tail;
            head = nullptr;
            tail = nullptr;
        }
        else {
            Node* prevNode = head; 

            for (int i = 0; i < nodesCount - 2; ++i) {
                prevNode = prevNode->next; // таким образом получаем предпоследний узел
            }

            delete tail;
            tail = prevNode; 
            tail->next = nullptr;
        }

        nodesCount--;
    }


    void insert(int pos, int val) {
        if (pos < 0 || pos > nodesCount) {
            std::cout << "Invalid position" << std::endl;
            return;
        }

        if (pos == 0) {
            push_front(val);
            return;
        }

        Node* newNode = new Node(val);
        // исп-ем как итератор, указывающий на начало списка
        Node* prevNode = head;

        for (int i = 0; i < pos - 1; ++i) {
            prevNode = prevNode->next;
        }

        // prevNode->next указывал как раз на эл-т, на который должна указывать новая нода
        newNode->next = prevNode->next; 
        prevNode->next = newNode; // теперь предыдущая нода указывает на новую 

        if (newNode->next == nullptr) {
            tail = newNode; // обновляем tail, если вставляем в конец
        }

        nodesCount++;
    }

    void print_list() const {
        Node* current = head;
        while (current != nullptr) {
            std::cout << current->val << " -> ";
            current = current->next;
        }
        std::cout << "nullptr" << std::endl;
    }
};

int main()
{
    setlocale(LC_ALL, "ru");

    LinkedList list;

    list.push_front(20);
    list.push_front(10);
    list.push_back(30);
    list.push_back(40);

    std::cout << "Исходный список: "; 
    list.print_list(); // 10 -> 20 -> 30 -> 40 -> nullptr

    list.insert(2, 25); 
    std::cout << "Список после insert(2, 25): ";
    list.print_list(); // 10 -> 20 -> 25 -> 30 -> 40 -> nullptr

    list.pop_front();
    std::cout << "Список после pop_front: ";
    list.print_list(); // 20 -> 25 -> 30 -> 40 -> nullptr

    list.pop_back();
    std::cout << "Список после pop_back: ";
    list.print_list(); // 20 -> 25 -> 30 -> nullptr

    return 0;
}

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
