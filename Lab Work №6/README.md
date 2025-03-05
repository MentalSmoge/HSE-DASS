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
