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