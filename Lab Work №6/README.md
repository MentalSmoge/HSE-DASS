# Лабораторная работа №6

Тема: Использование шаблонов проектирования

Цель работы: Получить опыт применения шаблонов проектирования при написании кода программной системы.

## Шаблоны проектирования GoF

### Порождающие шаблоны
<Представить с пояснения по каждому шаблону, указав название и назначение, сопроводив UML-диаграммой и соответствующим фрагментом программного кода>
#### Prototype
Компоненты в React приложениях обычно хранятся в виде готовых объектов, которые копируют и модифицируют по надобности
```js

export function Board() {
	const [elements, setElements] = useState([]);

	useEffect(() => {
...
    return (
		<div>
			<button onClick={addRectangle}>Add Rectangle</button>
			<button onClick={() => deleteRectangle(elements[0]?.id)}>
				Delete Rectangle
			</button>
			<button
				onClick={() =>
					paintRectangle(
						elements[Math.floor(Math.random() * elements.length)]
					)
				}
			>
				Paint Rectangle
			</button>
			<button onClick={() => printDebugElements()}>
				Debug all Elements
			</button>
			<Stage width={window.innerWidth} height={window.innerHeight}>
				<Layer>
					{elements.map((element) =>
						element.type === "text" ? (
							<Text key={element.id} {...element} />
						) : element.type === "rect" ? (
							<Rect
								key={element.id}
								id={element.id}
								x={element.x}
								y={element.y}
								fill={element.fill}
								height={element.height}
								width={element.width}
								draggable
								shadowColor="black"
								shadowBlur={10}
								shadowOpacity={0.6}
								shadowOffsetX={element.isDragging ? 10 : 5}
								shadowOffsetY={element.isDragging ? 10 : 5}
								scaleX={element.isDragging ? 1.2 : 1}
								scaleY={element.isDragging ? 1.2 : 1}
								onDragStart={handleDragStart}
								onDragEnd={handleDragEnd}
							/>
						) : null
					)}
				</Layer>
			</Stage>
		</div>
	);
}
```
#### Singleton
Служит для того, чтобы гарантировать наличие только единственного экземпляра класса
```js
export class RethinkDBElementRepository implements ElementRepository {
    private static instance: RethinkDBElementRepository;
    private connection: r.Connection;

    // Приватный конструктор
    private constructor(connection: r.Connection) {
        this.connection = connection;
    }

    // Статический метод для получения экземпляра
    public static async getInstance(): Promise<RethinkDBElementRepository> {
        if (!RethinkDBElementRepository.instance) {
            const connection = await r.connect({
                host: rethinkConfig.host,
                port: rethinkConfig.port,
            });
            RethinkDBElementRepository.instance = new RethinkDBElementRepository(connection);
            await RethinkDBElementRepository.instance.initialize();
        }
        return RethinkDBElementRepository.instance;
    }
```
### Factory method
Предоставляет интерфейс для создания объектов, но позволяет подклассам самим определить, какой класс инициализировать
```js
class ElementFactory {
    public static createElement(type: string): Element {
        if (type === "rect") {
            return new Rect();
        } else if (type === "circle") {
            return new Circle();
        }
        throw new Error("Invalid element type");
    }
}
```
### Структурные шаблоны
#### Composite
Позволяет обращаться к группе объектов, как к одному объекту - как пользователь воспринимает это, так и со стороны кода. Одинаковый интерфейс для группы и отдельных объектов.
```js
class Group implements Element {
    private children: Element[] = [];

    add(child: Element): void {
        this.children.push(child);
    }

    remove(child: Element): void {
        this.children = this.children.filter(c => c !== child);
    }

    draw(): void {
        this.children.forEach(child => child.draw());
    }

    move(x: number, y: number): void {
        this.children.forEach(child => child.move(x, y));
    }
}
```
#### Facade
Предоставляет общий API для взаимодействия с системой. Прежде всего на ум приходит API Gateway, который инкапсилирует всё общение с микросервисами в один общий API.
```js
import { AuthService } from "../services/authService";
import { UserService } from "../services/userService";
import { NotificationService } from "../services/notificationService";

export class ApiGatewayFacade {
    private authService: AuthService;
    private userService: UserService;
    private notificationService: NotificationService;

    constructor() {
        this.authService = new AuthService();
        this.userService = new UserService();
        this.notificationService = new NotificationService();
    }

    async login(username: string, password: string): Promise<string> {
        return this.authService.login(username, password);
    }

    async getUserProfile(userId: string): Promise<any> {
        return this.userService.getProfile(userId);
    }

    async sendNotification(userId: string, message: string): Promise<void> {
        return this.notificationService.sendNotification(userId, message);
    }
}
...
//потом в роутере
...
router.post("/login", async (req, res) => {
    const { username, password } = req.body;
    try {
        const token = await apiGateway.login(username, password);
        res.json({ token });
    } catch (error) {
        res.status(401).json({ error: "Invalid credentials" });
    }
});
...
```
#### Proxy
Позволяет отложить загрузку тяжелых объектов на попозже, когда нужно будет их отрисовать, не нагружая систему при открытии доски.
```js
interface Graphic {
    draw(): void;
}
...
class Image implements Graphic {
    constructor(private filename: string) {
        this.loadImage();
    }

    private loadImage(): void {
    	//Implement
    }

    draw(): void {
        console.log(`Drawing image: ${this.filename}`);
    }
}
...
class ImageProxy implements Graphic {
    private realImage: Image | null = null;

    constructor(private filename: string) {}

    draw(): void {
        if (this.realImage === null) {
            this.realImage = new HeavyImage(this.filename);
        }
        this.realImage.draw();
    }
}
```
### Поведенченские шаблоны
#### Observer
Объект наблюдает за изменениями, и в их случае оповещает об этом все подписанные объекты
```js
import { Server, Socket } from "socket.io";
import { ElementService, ElementDTO } from "../application/element_service";

export class WebSocketController {
    constructor(
        private io: Server,
        private elementService: ElementService
    ) {
        this.setupHandlers();
    }

    private setupHandlers(): void {
        this.io.on("connection", (socket: Socket) => {
            console.log("User connected:", socket.id);

            // Отправка текущего состояния
            socket.emit("board-state", this.elementService.getElements());

            // Создание элемента
            socket.on("element-create", async (element: ElementDTO) => {
                await this.elementService.createElement(element);
                this.io.emit("element-created", element);
            });

            // Обновление элемента
            socket.on("element-update", async (element: ElementDTO) => {
                await this.elementService.updateElement(element);
                this.io.emit("element-updated", element);
            });

            // Удаление элемента
            socket.on("element-delete", async (elementId: string) => {
                await this.elementService.deleteElement(elementId);
                this.io.emit("element-deleted", elementId);
            });

            socket.on("disconnect", () => {
                console.log("User disconnected:", socket.id);
            });
        });
    }
}
```
### Command
Позволяет инкапсулировать запросы в виде объектов, позволяя легко реализовать отмену изменений.
```js
interface Command {
    execute(): Promise<void>;
    undo(): Promise<void>;
}
...
import { Element } from "../domain/element";
import { RethinkDBElementRepository } from "../infrastructure/elements_repository";

export class CreateElementCommand implements Command {
    private element: Element;

    constructor(private repository: RethinkDBElementRepository, element: Element) {
        this.element = element;
    }

    async execute(): Promise<void> {
        await this.repository.saveElement(this.element);
        console.log(`Element created: ${this.element.id}`);
    }

    async undo(): Promise<void> {
        await this.repository.deleteElement(this.element.id);
        console.log(`Element creation undone: ${this.element.id}`);
    }
}
...
export class CommandManager {
    private history: Command[] = [];

    async executeCommand(command: Command): Promise<void> {
        await command.execute();
        this.history.push(command);
    }

    async undoLastCommand(): Promise<void> {
        const command = this.history.pop();
        if (command) {
            await command.undo();
        } else {
            console.log("No commands to undo.");
        }
    }
}
```
### State 
Позволяет объектам менять своё поведение в зависимости от своего состояния. Например, на доске объекты могут быть в разных состояниях:
```js
interface ElementState {
    clicked(user): void;
    edit(user): void;
    clickedAway(user): void;
    draw(): void;
    currentUser: void;
}
...
class DefaultState implements ElementState {
    clicked(user): void {
	currentUser = user
        //Перемещение в SelectedState
    }

    edit(user): void {
        //Пусто
    }

    clickedAway(user): void {
        //Пусто
    }

    draw(): void {
        //Дефолтное отображение
    }
}
class SelectedState implements ElementState {
    clicked(user): void {
        //Пусто
    }

    edit(user): void {
	if user == currentUser:
        //Перемещение в EditingState
    }

    clickedAway(): void {
	if user == currentUser:
        //Перемещение в DefaultState.");
    }

    draw(): void {
        //Отображение объекта в хайлайте
    }
}
class EditingState implements ElementState {
    clicked(): void {
    
    }

    edit(): void {
	if user == currentUser:
    	//Логика изменения
    }

    clickedAway(): void {
    //Перемещение в DefaultState
    }

    draw(): void {
	//Отображение объекта в хайлайте
    }
}
```
### Chain of Responsibility
Позволяет дать шанс обработать запрос сразу нескольким объектам. Тем самым позволяет разъединить посылателя запроса с его принимателем.
```js
interface Handler {
    setNext(handler: Handler): Handler;
    handle(request: any): void;
}
...
abstract class AbstractHandler implements Handler {
    private nextHandler: Handler | null = null;

    setNext(handler: Handler): Handler {
        this.nextHandler = handler;
        return handler;
    }

    handle(request: any): void {
        if (this.nextHandler) {
            this.nextHandler.handle(request);
        }
    }
}
...
class ElementClickHandler extends AbstractHandler {
    handle(request: any): void {
        if (request.type === "element") {
            console.log("Element click handled");
        } else {
            super.handle(request);
        }
    }
}

class GroupClickHandler extends AbstractHandler {
    handle(request: any): void {
        if (request.type === "group") {
            console.log("Group click handled");
        } else {
            super.handle(request);
        }
    }
}

class BackgroundClickHandler extends AbstractHandler {
    handle(request: any): void {
        if (request.type === "background") {
            console.log("Background click handled");
        } else {
            super.handle(request);
        }
    }
}
...
const elementHandler = new ElementClickHandler();
const groupHandler = new GroupClickHandler();
const backgroundHandler = new BackgroundClickHandler();

elementHandler.setNext(groupHandler).setNext(backgroundHandler);
```
