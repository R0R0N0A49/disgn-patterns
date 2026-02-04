# disgn-patterns
|Pattern de creation|use|Pattern structurel| Pattern Conportementaux|
|-------------------|---|------------------|------------------------|
|[Singleton](#Singleton)||[Decorator](#Decorator)|[Command](#Command)|
|[Builder](#Builder)||[Bridge](#Bridge)|[Memento](#Memento)|
|[Prototype](#Prototype)||[Composite](#Conposite)|[Observer](#Observer)|
|[Factory Methode](#Factory-Methode)||[Facades](#Facades)|[Strategy](#Strategy)|
|||[FlyWeight](#FlyWeight)|[Chain-of-Responsability](#Chain-of-Responsability)|
|||[Proxy](#Proxy)||

## Pattern de Creation :

1. ### Singleton
    garantir l'unicité d'une instance et fournir son point d'accès

***Exemple :***
```c++
#include <iostream>

class Singleton {
public:
    // Méthode pour accéder à l'instance unique
    static Singleton& getInstance() {
        static Singleton instance; // Instanciation unique
        return instance;
    }
    // Méthode publique de l'objet singleton
    void showMessage() const {
        std::cout << "Je suis un singleton !" << std::endl;
    }
    // Supprime les méthodes de copie et d'affectation
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;
private:
    Singleton() {
        std::cout << "Constructeur du Singleton appelé." << std::endl;
    }
    ~Singleton() {
        std::cout << "Destructeur du Singleton appelé." << std::endl;
    }
};

int main() {
    // Accéder au singleton
    Singleton& s1 = Singleton::getInstance();
    s1.showMessage();

    // Vérification : toujours la même instance
    Singleton& s2 = Singleton::getInstance();
    if (&s1 == &s2) {
        std::cout << "s1 et s2 pointent vers la même instance." << std::endl;
    }

    return 0;
}
```
[HOME](#disgn-patterns)

2. ### Builder
   Construire des objets complexes en plusieurs étapes avec le même code, extraire les paramètres non obligatoires
   
***Exemple :***
```c++
#include <iostream>
#include <string>

// Produit
class Pizza {
    public:
        std::string dough;
        std::string sauce;
        std::string topping;

        void show() const {
            std::cout << "Pizza with " << dough << ", " << sauce << ", and " << topping << ".\n";
        }
};

class PizzaBuilder {
    protected:
        Pizza* pizza;
    public:
        PizzaBuilder() { pizza = new Pizza(); }
        ~PizzaBuilder() { delete pizza; }

        Pizza* getPizza() { return pizza; }
        void reset() { pizza = new Pizza(); }

        virtual void buildDough() = 0;
        virtual void buildSauce() = 0;
        virtual void buildTopping() = 0;
};

class HawaiianPizzaBuilder : public PizzaBuilder {
    public:
        void buildDough() override { pizza->dough = "Soft dough"; }
        void buildSauce() override { pizza->sauce = "Sweet sauce"; }
        void buildTopping() override { pizza->topping = "Ham and Pineapple"; }
};

// Director
class PizzaDirector {
        PizzaBuilder* builder;
    public:
        void setBuilder(PizzaBuilder* b) { builder = b; }

        Pizza* makePizza() {
            builder->reset();
            builder->buildDough();
            builder->buildSauce();
            builder->buildTopping();
            return builder->getPizza();
        }
};

int main() {
    PizzaDirector director;
    HawaiianPizzaBuilder builder;

    director.setBuilder(&builder);
    Pizza* pizza = director.makePizza();

    pizza->show(); // Output: Pizza with Soft dough, Sweet sauce, and Ham and Pineapple.

    delete pizza;
    return 0;
}
```
[HOME](#disgn-patterns)

3. ### Prototype
    copy les données d'une classe sans connaître la classe
   
***Exemple :***
```c++
#include <iostream>
#include <string>
#include <memory>

class Shape {
    public:
        virtual ~Shape() = default;
        virtual std::unique_ptr<Shape> clone() const = 0;
        virtual void draw() const = 0;
};

class Circle : public Shape {
        std::string color;
    public:
        Circle(const std::string& c) : color(c) {}
    
        std::unique_ptr<Shape> clone() const override {
            return std::make_unique<Circle>(*this); // Copie l'objet
        }

        void draw() const override {
            std::cout << "Drawing a " << color << " circle.\n";
        }
};

class Rectangle : public Shape {
        std::string color;
    public:
        Rectangle(const std::string& c) : color(c) {}
    
        std::unique_ptr<Shape> clone() const override {
            return std::make_unique<Rectangle>(*this); // Copie l'objet
        }

        void draw() const override {
            std::cout << "Drawing a " << color << " rectangle.\n";
        }
};

int main() {
    Circle redCircle("red");
    Rectangle blueRectangle("blue");

    auto anotherCircle = redCircle.clone();
    auto anotherRectangle = blueRectangle.clone();

    redCircle.draw();          // Output: Drawing a red circle.
    blueRectangle.draw();      // Output: Drawing a blue rectangle.
    anotherCircle->draw();     // Output: Drawing a red circle.
    anotherRectangle->draw();  // Output: Drawing a blue rectangle.

    return 0;
}
```
[HOME](#disgn-patterns)

4. ### Factory-Methode
   permet de cree une classe sans savoir exactement quelle classe est cree

   ***Exemple :***
   ```c++
    #include <iostream>
    #include <memory>
    #include <stdexcept>
    
    // 1️⃣ Interface (classe abstraite)
    class Paiement {
    public:
        virtual void payer() = 0;
        virtual ~Paiement() = default;
    };
    
    // 2️⃣ Implémentations concrètes
    class PaiementCarte : public Paiement {
    public:
        void payer() override {
            std::cout << "Paiement par carte" << std::endl;
        }
    };
    
    class PaiementPaypal : public Paiement {
    public:
        void payer() override {
            std::cout << "Paiement par PayPal" << std::endl;
        }
    };
    
    class PaiementVirement : public Paiement {
    public:
        void payer() override {
            std::cout << "Paiement par virement" << std::endl;
        }
    };
    
    // 3️⃣ Factory Method
    class PaiementFactory {
    public:
        static std::unique_ptr<Paiement> creerPaiement(const std::string& type) {
            if (type == "carte")
                return std::make_unique<PaiementCarte>();
            if (type == "paypal")
                return std::make_unique<PaiementPaypal>();
            if (type == "virement")
                return std::make_unique<PaiementVirement>();
    
            throw std::invalid_argument("Type de paiement inconnu");
        }
    };
    
    // 4️⃣ Utilisation
    int main() {
        auto paiement = PaiementFactory::creerPaiement("paypal");
        paiement->payer();
        return 0;
    }```

## Patron Structurel

1. ### Decorator
    Ajoute dynamiquement des comportements à une instance
   
***Exemple :***
```c++
#include <iostream>
#include <string>

class Beverage {
    public:
        virtual ~Beverage() = default;
        virtual std::string getDescription() const = 0;
        virtual double cost() const = 0;
};

class Coffee : public Beverage {
    public:
        std::string getDescription() const override {
            return "Coffee";
        }
        double cost() const override {
            return 2.0; // Prix d'un café
        }
};

class BeverageDecorator : public Beverage {
    protected:
        Beverage* beverage;
    public:
        BeverageDecorator(Beverage* bev) : beverage(bev) {}
        virtual ~BeverageDecorator() { delete beverage; }
    
        virtual std::string getDescription() const override {
            return beverage->getDescription();
        }
        virtual double cost() const override {
            return beverage->cost();
        }
};

    class MilkDecorator : public BeverageDecorator {
    public:
        MilkDecorator(Beverage* bev) : BeverageDecorator(bev) {}
    
        std::string getDescription() const override {
            return beverage->getDescription() + ", Milk";
        }
        double cost() const override {
            return beverage->cost() + 0.5; // Ajouter le coût du lait
        }
};

class SugarDecorator : public BeverageDecorator {
    public:
        SugarDecorator(Beverage* bev) : BeverageDecorator(bev) {}

        std::string getDescription() const override {
            return beverage->getDescription() + ", Sugar";
        }
        double cost() const override {
            return beverage->cost() + 0.3; // Ajouter le coût du sucre
        }
};

int main() {
    Beverage* myCoffee = new Coffee();  // Café simple
    std::cout << myCoffee->getDescription() << " $" << myCoffee->cost() << "\n";

    // Décorer avec du lait
    myCoffee = new MilkDecorator(myCoffee);
    std::cout << myCoffee->getDescription() << " $" << myCoffee->cost() << "\n";

    // Décorer avec du sucre
    myCoffee = new SugarDecorator(myCoffee);
    std::cout << myCoffee->getDescription() << " $" << myCoffee->cost() << "\n";

    delete myCoffee; // Libération de la mémoire
    return 0;
}
```
[HOME](#disgn-patterns)

2. ### Bridge
   crée des hiérarchies de classes indépendantes
   
***Exemple :***
```c++
#include <iostream>
#include <string>

class Device {
    public:
        virtual ~Device() = default;
        virtual void turnOn() = 0;
        virtual void turnOff() = 0;
};

class TV : public Device {
    public:
        void turnOn() override {
            std::cout << "TV is now ON\n";
        }

        void turnOff() override {
            std::cout << "TV is now OFF\n";
        }
};

class Radio : public Device {
    public:
        void turnOn() override {
            std::cout << "Radio is now ON\n";
        }

        void turnOff() override {
            std::cout << "Radio is now OFF\n";
        }
};

class RemoteControl {
    protected:
        Device* device;
    public:
        RemoteControl(Device* dev) : device(dev) {}
        virtual ~RemoteControl() { delete device; }

        virtual void pressPowerButton() = 0;
};

class SimpleRemote : public RemoteControl {
    public:
        SimpleRemote(Device* dev) : RemoteControl(dev) {}

        void pressPowerButton() override {
            std::cout << "Pressing power button...\n";
            device->turnOn();
        }
};

class AdvancedRemote : public RemoteControl {
    public:
        AdvancedRemote(Device* dev) : RemoteControl(dev) {}

        void pressPowerButton() override {
            std::cout << "Pressing power button...\n";
            device->turnOn();
        }

        void mute() {
            std::cout << "Muting the device\n";
        }
};

int main() {
    Device* tv = new TV();
    Device* radio = new Radio();

    RemoteControl* remote = new SimpleRemote(tv);
    remote->pressPowerButton(); // Allumer la TV

    remote = new AdvancedRemote(radio);
    remote->pressPowerButton(); // Allumer la Radio
    static_cast<AdvancedRemote*>(remote)->mute(); // Mute la radio

    delete remote;
    return 0;
}
```
[HOME](#disgn-patterns)

3. ### Conposite
    Agence des objets en arborescence de manière à traiter un élément de manière individuelle (demande aux objets eux-mêmes d'interagir avec leurs sous-éléments)
  
***Exemple :***
```c++
#include <iostream>
#include <vector>
#include <string>

class FileSystemComponent {
public:
    virtual ~FileSystemComponent() = default;
    virtual void display(int depth = 0) const = 0;
};

class File : public FileSystemComponent {
        std::string name;
    public:
        File(const std::string& name) : name(name) {}

        void display(int depth = 0) const override {
            for (int i = 0; i < depth; ++i) std::cout << "-";
                std::cout << " File: " << name << "\n";
        }
};

class Directory : public FileSystemComponent {
        std::string name;
        std::vector<FileSystemComponent*> children;

    public:
        Directory(const std::string& name) : name(name) {}
    
        void add(FileSystemComponent* component) {
            children.push_back(component);
        }
        void remove(FileSystemComponent* component) {
            children.erase(std::remove(children.begin(), children.end(), component), children.end());
        }
        void display(int depth = 0) const override {
            for (int i = 0; i < depth; ++i) std::cout << "-";
            std::cout << " Directory: " << name << "\n";
            for (const auto& child : children) {
                child->display(depth + 1);  // Affichage récursif
            }
        }
        ~Directory() {
            for (auto* child : children) {
                delete child;  // Libération de la mémoire
            }
        }
};

int main() {
    FileSystemComponent* file1 = new File("File1.txt");
    FileSystemComponent* file2 = new File("File2.txt");
    FileSystemComponent* file3 = new File("File3.txt");

    Directory* dir1 = new Directory("Dir1");
    Directory* dir2 = new Directory("Dir2");

    dir1->add(file1);
    dir1->add(file2);
    dir2->add(file3);

    dir1->add(dir2);

    dir1->display();

    delete dir1;

    return 0;
}
```
[HOME](#disgn-patterns)

4. ### Facades
   fournit une interface devant des fonctionaliter complex (facilite l'acces a une fonction d'une biblihoteque complex a l'aide d'une autre fonction)
   
***Exemple :***
```c++
#include <iostream>
#include <string>

// Sous-système 1 : Système de son
class SoundSystem {
    public:
        void turnOn() {
            std::cout << "Sound system is ON.\n";
        }
        void turnOff() {
            std::cout << "Sound system is OFF.\n";
        }
        void setVolume(int level) {
            std::cout << "Setting sound volume to " << level << ".\n";
        }
};

class Projector {
    public:
        void turnOn() {
            std::cout << "Projector is ON.\n";
        }
        void turnOff() {
            std::cout << "Projector is OFF.\n";
        }
        void setScreenSize(int size) {
            std::cout << "Setting projector screen size to " << size << " inches.\n";
        }
};

class Lights {
    public:
        void dim(int level) {
            std::cout << "Dimming lights to " << level << " percent.\n";
        }
        void turnOn() {
            std::cout << "Lights are ON.\n";
        }
};

class HomeTheaterFacade {
        SoundSystem soundSystem;
        Projector projector;
        Lights lights;
    public:
        void watchMovie() {
            std::cout << "Get ready to watch a movie...\n";
             lights.dim(10);
            soundSystem.turnOn();
            soundSystem.setVolume(8);
            projector.turnOn();
            projector.setScreenSize(120);
        }

        void endMovie() {
            std::cout << "Turning off movie setup...\n";
            lights.turnOn();
            soundSystem.turnOff();
            projector.turnOff();
        }
};

int main() {
    HomeTheaterFacade homeTheater;
    
    homeTheater.watchMovie();
    homeTheater.endMovie();

    return 0;
}
```
[HOME](#disgn-patterns)

5. ### FlyWeight
    Permet de stocker plus d'objets dans la RAM en optimisant l'espace utilisé. Crée une seule fois les parties d'un objet qui ne changent pas.
   
***Exemple :***
```c++
#include <iostream>
#include <unordered_map>
#include <string>

class Character {
    public:
        virtual ~Character() = default;
        virtual void display() const = 0;
};

class ConcreteCharacter : public Character {
        char symbol;  // Le caractère réel
    public:
        ConcreteCharacter(char s) : symbol(s) {}
        void display() const override {
        std::cout << symbol;
        }
};

class CharacterFactory {
        std::unordered_map<char, Character*> characters;
    public:
        ~CharacterFactory() {
            for (auto& entry : characters) {
                delete entry.second;  // Libération des objets créés
            }
        }
         Character* getCharacter(char symbol) {
            if (characters.find(symbol) == characters.end()) {
                characters[symbol] = new ConcreteCharacter(symbol);
                std::cout << "Creating character: " << symbol << "\n";
            }
            return characters[symbol];
        }
};

int main() {
    CharacterFactory factory;

    // Représentation d'un texte en utilisant des objets partagés
    std::string text = "Flyweight Pattern";
    
    for (char c : text) {
        Character* character = factory.getCharacter(c);
        character->display();  // Affichage du caractère
    }

    std::cout << "\n";
    return 0;
}
```
[HOME](#disgn-patterns)

7. ### Proxy
   Utiliser un substitut d'un objet directement. On peut ajouter des opérations avant et après les appels.
   
***Exemple :***
```c++
#include <iostream>
#include <string>

class RealSubject {
    public:
        void request() {
            std::cout << "RealSubject: Handling request.\n";
       }
};

class Proxy {
        RealSubject* realSubject;
    public:
        Proxy() : realSubject(nullptr) {}
        ~Proxy() {
            delete realSubject;
        }
        void request() {
            std::cout << "Proxy: Pre-processing request.\n"; 
            if (!realSubject) {
                realSubject = new RealSubject(); // Crée l'objet réel à la demande (Lazy Loading)
                std::cout << "Proxy: RealSubject is created.\n";
            }
            realSubject->request();
            std::cout << "Proxy: Post-processing request.\n";
        }
};

int main() {
    Proxy proxy;
    proxy.request();  // Proxy gère l'accès au RealSubject
    return 0;
}
```
[HOME](#disgn-patterns)

## Conportementaux

1. ## command
    Représentation des actions par des commandes stockées, exécutées, annulées, etc...
   
***Exemple :***
```c++
#include <iostream>
#include <vector>
#include <memory>

class Command {
    public:
        virtual ~Command() = default;
        virtual void execute() const = 0;
        virtual void undo() const = 0;
};

class Light {
    public:
        void turnOn() const {
            std::cout << "Light is ON.\n";
        }
        void turnOff() const {
            std::cout << "Light is OFF.\n";
        }
};

class TurnOnLightCommand : public Command {
        Light& light;
    public:
        TurnOnLightCommand(Light& l) : light(l) {}
        void execute() const override {
            light.turnOn();
        }
        void undo() const override {
            light.turnOff();
        }
};

class TurnOffLightCommand : public Command {
        Light& light;
    public:
        TurnOffLightCommand(Light& l) : light(l) {}
    
        void execute() const override {
            light.turnOff();
        }
        void undo() const override {
            light.turnOn();
        }
};

class RemoteControl {
        std::vector<std::shared_ptr<Command>> commandHistory;
    public:
        void pressButton(const Command& command) {
            command.execute();
            commandHistory.push_back(std::make_shared<Command>(command));  // Sauvegarde de l'historique des commandes
        }
        void pressUndo() {
            if (!commandHistory.empty()) {
                auto lastCommand = commandHistory.back();
                lastCommand->undo();
                commandHistory.pop_back();  // Supprime la dernière commande
            } else {
                std::cout << "No command to undo.\n";
            }
        }
};

int main() {
    Light livingRoomLight;

    TurnOnLightCommand turnOn(livingRoomLight);
    TurnOffLightCommand turnOff(livingRoomLight);

    RemoteControl remote;

    remote.pressButton(turnOn);  // Allumer la lumière
    remote.pressButton(turnOff); // Éteindre la lumière

    remote.pressUndo();  // Retourne à l'état précédent (allumer la lumière)

    return 0;
}
```
[HOME](#disgn-patterns)

2. ### Memento
    faire une sauvegarde de l'état d'un objet et restaurer un objet sans connaître la structure interne de l'objet
   
***Exemple :***
```c++
#include <iostream>
#include <string>
#include <memory>

class Document {
    std::string content;
public:
    void setContent(const std::string& newContent) {
        content = newContent;
    }
    std::string getContent() const {
        return content;
    }

    class Memento {
            friend class Document;
            std::string savedContent;
            Memento(const std::string& content) : savedContent(content) {}
        public:
            std::string getSavedContent() const {
                return savedContent;
            }
        };

        std::shared_ptr<Memento> createMemento() const {
            return std::make_shared<Memento>(content);
        }
        void restore(const std::shared_ptr<Memento>& memento) {
            content = memento->getSavedContent();
        }
};

class DocumentHistory {
        std::shared_ptr<Document::Memento> memento;
    public:
        void save(const std::shared_ptr<Document::Memento>& m) {
            memento = m;
        }
        std::shared_ptr<Document::Memento> getMemento() const {
            return memento;
        }
};

int main() {
    Document doc;

    doc.setContent("Version 1 of the document.");
    std::cout << "Document content: " << doc.getContent() << std::endl;

    DocumentHistory history;
    history.save(doc.createMemento());

    doc.setContent("Version 2 of the document.");
    std::cout << "Document content: " << doc.getContent() << std::endl;

    doc.restore(history.getMemento());
    std::cout << "Document content after undo: " << doc.getContent() << std::endl;

    return 0;
}
```
[HOME](#disgn-patterns)

4. ### Observer
    préviens qu'un événement vient de se produire
   
***Exemple :***
``` c++
#include <iostream>
#include <vector>
#include <memory>

class Subject {
    public:
        virtual ~Subject() = default;
        virtual void addObserver(class Observer* observer) = 0;
        virtual void removeObserver(class Observer* observer) = 0;
        virtual void notifyObservers() = 0;
};

class Observer {
    public:
        virtual ~Observer() = default;
        virtual void update(const std::string& state) = 0;
};

class ConcreteSubject : public Subject {
        std::vector<Observer*> observers;
        std::string state;
    public:
        void addObserver(Observer* observer) override {
            observers.push_back(observer);
        }
        void removeObserver(Observer* observer) override {
            observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
        }
        void notifyObservers() override {
            for (auto observer : observers) {
                observer->update(state);  // Notify each observer
            }
        }
        void setState(const std::string& newState) {
            state = newState;
            notifyObservers();  // Notify observers when state changes
        }
        std::string getState() const {
            return state;
        }
};

class ConcreteObserver : public Observer {
        std::string name;
    public:
        ConcreteObserver(const std::string& observerName) : name(observerName) {}
        void update(const std::string& state) override {
            std::cout << "Observer " << name << " updated with state: " << state << std::endl;
        }
};

int main() {
    ConcreteSubject subject;

    ConcreteObserver observer1("Observer1");
    ConcreteObserver observer2("Observer2");

    subject.addObserver(&observer1);
    subject.addObserver(&observer2);

    subject.setState("State 1");
    subject.setState("State 2");

    subject.removeObserver(&observer2);
    
    subject.setState("State 3");

    return 0;
}

```
[HOME](#disgn-patterns)

6. ### Strategy
    famille d'algorithmes utilisable et interchangeable dynamiquement
   
***Exemple :***
```c++
#include <iostream>
#include <memory>

class TaxStrategy {
    public:
        virtual ~TaxStrategy() = default;
        virtual double calculateTax(double amount) const = 0;
};

class StandardTaxStrategy : public TaxStrategy {
    public:
        double calculateTax(double amount) const override {
            return amount * 0.1;  // 10% de taxe
        }
};

class ReducedTaxStrategy : public TaxStrategy {
    public:
        double calculateTax(double amount) const override {
            return amount * 0.05;  // 5% de taxe
        }
};

class NoTaxStrategy : public TaxStrategy {
    public:
        double calculateTax(double amount) const override {
            return 0.0;  // Pas de taxe
        }
};

class TaxCalculator {
        std::shared_ptr<TaxStrategy> strategy;
    public:
        TaxCalculator(std::shared_ptr<TaxStrategy> taxStrategy) : strategy(taxStrategy) {}
        void setStrategy(std::shared_ptr<TaxStrategy> newStrategy) {
            strategy = newStrategy;
        }
        double calculate(double amount) const {
            return strategy->calculateTax(amount);
        }
};

int main() {
    TaxCalculator calculator(std::make_shared<StandardTaxStrategy>());

    double amount = 100.0;
    std::cout << "Tax on " << amount << " with standard tax: " << calculator.calculate(amount) << std::endl;

    calculator.setStrategy(std::make_shared<ReducedTaxStrategy>());
    std::cout << "Tax on " << amount << " with reduced tax: " << calculator.calculate(amount) << std::endl;

    calculator.setStrategy(std::make_shared<NoTaxStrategy>());
    std::cout << "Tax on " << amount << " with no tax: " << calculator.calculate(amount) << std::endl;

    return 0;
}
```
[HOME](#disgn-patterns)

7. ### Chain-of-Responsability
   permet à une requête d'être traitée par une série d'objets, où chaque objet a la possibilité de traiter la requête ou de la transmettre à l'objet suivant dans la chaîne
   
***Exemple :***
```c++
#include <iostream>
#include <memory>
#include <string>

class Handler {
protected:
        std::shared_ptr<Handler> nextHandler;  // Prochain gestionnaire dans la chaîne
    public:
        virtual ~Handler() = default;
        void setNextHandler(std::shared_ptr<Handler> handler) {
            nextHandler = handler;
        }
        // Méthode abstraite pour traiter la demande
        virtual void handleRequest(double amount) = 0;
};

class EmployeeHandler : public Handler {
    public:
        void handleRequest(double amount) override {
            if (amount <= 100) {
                std::cout << "Employee approved the reimbursement of: $" << amount << std::endl;
            } else if (nextHandler) {
                nextHandler->handleRequest(amount);
            }
        }
};

class ManagerHandler : public Handler {
    public:
        void handleRequest(double amount) override {
            if (amount <= 500) {
                std::cout << "Manager approved the reimbursement of: $" << amount << std::endl;
            } else if (nextHandler) {
                nextHandler->handleRequest(amount);
            }
        }
};

class DirectorHandler : public Handler {
    public:
        void handleRequest(double amount) override {
            if (amount <= 1000) {
                std::cout << "Director approved the reimbursement of: $" << amount << std::endl;
            } else {
                std::cout << "Amount too high. No one can approve this reimbursement." << std::endl;
            }
        }
};

int main() {
    std::shared_ptr<Handler> employee = std::make_shared<EmployeeHandler>();
    std::shared_ptr<Handler> manager = std::make_shared<ManagerHandler>();
    std::shared_ptr<Handler> director = std::make_shared<DirectorHandler>();

    // Configuration de la chaîne : employee -> manager -> director
    employee->setNextHandler(manager);
    manager->setNextHandler(director);

    double amount1 = 50;
    double amount2 = 300;
    double amount3 = 800;
    double amount4 = 1500;

    std::cout << "Request for amount: $" << amount1 << std::endl;
    employee->handleRequest(amount1);

    std::cout << "\nRequest for amount: $" << amount2 << std::endl;
    employee->handleRequest(amount2);

    std::cout << "\nRequest for amount: $" << amount3 << std::endl;
    employee->handleRequest(amount3);

    std::cout << "\nRequest for amount: $" << amount4 << std::endl;
    employee->handleRequest(amount4);

    return 0;
}
```
[HOME](#disgn-patterns)
