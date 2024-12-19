# Лабораторная работа №3
**Тема: Использование принципов проектирования на уровне методов и классов**

**Цель работы: Получить опыт проектирования и реализации модулей с использованием принципов KISS, YAGNI, DRY, SOLID и др.**
## Диаграмма контейнеров
![image](https://github.com/user-attachments/assets/8545586e-4553-46ff-9b84-c3fd1e950efc)
## Диаграмма компонентов
### Веб приложение
![image](https://github.com/user-attachments/assets/d2d5f1db-1a34-4e47-ad6d-275abdc638cd)

## Диаграмма последовательностей
Разберём use case - Экспорт проекта пользователем
![image](https://github.com/user-attachments/assets/835a4975-738a-4171-b8e9-fc05f65cbd22)
### Диаграмма последовательностей экспорта проекта
![image](https://github.com/user-attachments/assets/3281d6d4-5430-4120-a0a0-896a61ba1998)
В диаграмме показан экспорт проекта, и взаимодействие между пользователем и компонентами фронтенд программы.
<Представить диаграмму последовательностей с краткими пояснениями>
## Модель БД
![image](https://github.com/user-attachments/assets/bf235471-9b12-4bdd-81b3-797ceb87cd83)
Данная база данных содержит информацию про пользователей, а также проектов с досками и их содержимым. Она приведена к 3-ей нормальной форме (это выражается, например, в наличии суррогатной таблицы users_projects, в которой содержатся информация, какой пользователь с каким проектом имеет какую связь (owner, guest, ...))
## Применение основных принципов разработки

### KISS (Keep It Simple, Stupid)
```ts
export class BoardManager {
    project: Project;
    boards: { [key: number]: Board } = {};

    createBoard(title: string): Board {
        const board = new Board(title);
        this.boards[title] = board;
        return board;
    }
    deleteBoard(id: number): boolean {
        if (this.boards[id]) {
            delete this.boards[id];
            return true;
        }
        return false;
    }
}
```
BoardManager делает ровно то что ожидаешь - создает и удаляет доски из проекта.
### YAGNI (You Aren't Gonna Need It)
```ts
export class Board {
    title: string;
    elements: Element[];
    backgroundColor: string;

    constructor(title: string) {
        this.title = title;
        this.elements = [];
        this.backgroundColor = "white";
    }

    addElement(element: Element) {
        this.elements.push(element);
    }

    removeElement(element: Element) {
        this.elements = this.elements.filter(e => e !== element);
    }

    changeBackground(color: string) {
        this.backgroundColor = color;
    }
}
```
На данный момент, класс доски имеет такое простое наполнение наполнение - создание и удаление элементов, а также смена цвета задника. Никаких ненужных абстракций, никакого лишнего кода.
### DRY (Don't Repeat Yourself)
```ts
const addElement = (content: string) => {
        const newElement: Element = { content };
        undoManager.saveState(board);
        board.addElement(newElement);
    };
...
addElement("Замечательная идея")
addElement("Вторая идея, не такая хорошая")
```
В принципе, любое применение функции больше одного раза - уже воплощение принципа DRY. В данном случае мы добавляем два элемента, и используем для этого одну функцию
### SOLID
#### S - Single Responsibility Principle (Принцип единственной ответственности)
```ts
export class BoardManager {
    project: Project;
    boards: { [key: number]: Board } = {};

    createBoard(title: string): Board {
        const board = new Board(title);
        this.boards[title] = board;
        return board;
    }
    deleteBoard(id: number): boolean {
        if (this.boards[id]) {
            delete this.boards[id];
            return true;
        }
        return false;
    }
}
```
BoardManager отвечает только за работу досок в проекте, больше ни за что.
#### O - Open/Closed Principle (Принцип открытости/закрытости)

#### L - Liskov Substitution Principle (Принцип подстановки Лисков)
#### I - Interface Segregation Principle (Принцип разделения интерфейса)
#### D - Dependency Inversion Principle (Принцип инверсии зависимостей)

<Продемонстрировать фрагменты кода, пояснив какой принцип реализуется>
## Дополнительные принципы разработки
<По каждому принципу разработки из раздела повышенной сложности обосновать отказ или применение>
