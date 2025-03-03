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
