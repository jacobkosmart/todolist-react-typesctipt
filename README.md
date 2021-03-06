# π TodoList-DnD App

<a href="https://todo.jacobko.info/" target="_blank">Live Demo</a>

![Animation1](https://user-images.githubusercontent.com/28912774/130744544-a6e5951b-d804-4de4-921e-34295ab82961.gif)

## π» 1.νλ‘μ νΈ μκ°

### π μ¬μ©κΈ°μ  λ° μΈμ΄

- Create React App with TypeScript

- React Beautiful Dnd

- Sass

- PWA

- React Icons

### β° κ°λ° κΈ°κ°

2021-08-09 ~ 2021-08-13

## π 2.νλ‘μ νΈ λ΄μ©

### π μ£Όμ κΈ°λ₯

- TodoList CRUD

- 2κ°μ Category (To do, Completed) μ task box κ° drag and drop κ°λ₯ (list order λ λ³κ²½ κ°λ₯)

- localStorage μ DB μ μ₯

- PWA μ± λ°°ν¬ (λͺ¨λ°μΌ, λ°μ€ν¬ν μ΄ν μ¬μ©κ°λ₯)

- Fully responsive web design

### π μ€μΉ ν¨ν€μ§

```bash
# CRA
npx create-react-app PROJECT --template typescript

# node-sass
yarn add node-sass@4.14.1

# React icons
yarn add react-icons

# react-beautiful-dnd
yarn add react-beautiful-dnd
yarn add @types/react-beautiful-dnd
```

## π 3.μ£Όμ μ½λ

### A.CRUD with react hooks

```ts
// App.tsx

// State
const [todo, setTodo] = useState<string>("");
const [todos, setTodos] = useState<Todo[]>(() => {
  // Called from stored todos from localStorage
  const savedTodos = localStorage.getItem("todo");
  if (savedTodos) {
    return JSON.parse(savedTodos);
  } else {
    return [];
  }
});
const handleAdd = (e: React.FormEvent) => {
  e.preventDefault();
  if (todo) {
    setTodos([...todos, { id: Date.now(), todo, isDone: false }]);
    localStorage.setItem("todo", JSON.stringify(todos));

    // cleanUp todo
    setTodo("");
    localStorage.setItem("completedTodo", JSON.stringify(completedTodos));
  }
};

// in SingleTodo.tsx

// state Edit
const [edit, setEdit] = useState<boolean>(false);
const [editTodo, setEditTodo] = useState<string>(todo.todo);

// handleEdit function
const handleEdit = (e: React.FormEvent, id: number) => {
  e.preventDefault();

  setTodos(
    todos.map((todo) => (todo.id === id ? { ...todo, todo: editTodo } : todo))
  );
  setEdit(false);
};

// handleDone function
const handleDone = (id: number) => {
  setTodos(
    todos.map((todo) =>
      todo.id === id ? { ...todo, isDone: !todo.isDone } : todo
    )
  );
};

// handleDelete function
const handleDelete = (id: number) => {
  const ok = window.confirm("Are you sure delete the task?");
  if (ok) {
    setTodos(todos.filter((todo) => todo.id !== id));
  }
};
```

### B. React beautiful-dnd

```tsx
// App.tsx

 // drapable function
  // drag λ κ³³μ κ²°κ³Ό κ°μ λ°μμ state μ κ°μ μ μ₯νλ function
  const onDragEnd = (result: DropResult) => {
    // result λ‘ λμ΄μ€λ κ°νμΈ source : μλ μμΉ destination : drop λ λͺ©μ μ§λ₯Ό κ°λ¦¬ν΄
    // console.log(result);
    const { source, destination } = result;

    // destination μ΄ drop μ΄ κ°λ₯νμ§ μμ λ°μ λ¨μλ μλ¬΄μΌλ μΌμ΄λμ§ μκ²
    if (!destination) return;
    // κ°μ zone μ drop λμμλμ κ°μ index μΌλ μλ¬΄κ²λ μΌμ΄ λμ§ μκ² ν¨
    if (
      destination.droppableId === source.droppableId &&
      destination.index === source.index
    )
      return;

    // DnD logic
    let add,
      active = todos,
      complete = completedTodos;

    if (source.droppableId === "TodosList") {
      add = active[source.index];
      active.splice(source.index, 1);
    } else {
      add = complete[source.index];
      complete.splice(source.index, 1);
    }

    if (destination.droppableId === "TodosList") {
      active.splice(destination.index, 0, add);
    } else {
      complete.splice(destination.index, 0, add);
    }

    // dnd event λ°μκΈ° state μ μ μ₯ λ° localStorage μ data μ μ₯
    setCompletedTodos(complete);
    localStorage.setItem("completedTodo", JSON.stringify(completedTodos));
    setTodos(active);
    localStorage.setItem("todo", JSON.stringify(todos));
  };




// in ToDoList.tsx
// Droppable λ²μ μ€μ 

   return (
    <>
      <div className="container">
        <Droppable droppableId="TodosList">
          {(provided, snapshot) => (
            <div
              className="todos"
              ref={provided.innerRef}
              {...provided.droppableProps}
            >
              <span className="todos__heading">To Do</span>
              {todos.map((todo, index) => (
                <SingleTodo
                  index={index}
                  todo={todo}
                  todos={todos}
                  key={todo.id}
                  setTodos={setTodos}
                />
              ))}
              {/* make drapable area  */}
              {provided.placeholder}
            </div>
          )}
        </Droppable>
        <Droppable droppableId="TodosRemove">
          {(provided, snapshot) => (
            <div
              className={`remove ${
                snapshot.isDraggingOver ? "dragcomplete" : ""
              }`}
              ref={provided.innerRef}
              {...provided.droppableProps}
            >
              <span className="remove__heading">Completed</span>
              {completedTodos.map((todo, index) => (
                <SingleTodo
                  index={index}
                  todo={todo}
                  todos={completedTodos}
                  key={todo.id}
                  setTodos={setCompletedTodos}
                />
              ))}
              {/* make drapable area  */}
              {provided.placeholder}
            </div>
          )}
        </Droppable>
      </div>
    </>
  );
};

```

### C.PWA

> PWA λ§λ€κΈ° κ³Όμ  λ° κ΄λ ¨ λ΄μ© (Jacob's DevLog) - [https://jacobko.info/pwa/pwa/](https://jacobko.info/pwa/pwa/)

## π‘ 4. Reference

Type Reference - [https://flow.org/en/docs/react/types/](https://flow.org/en/docs/react/types/)

react-beautiful-dnd Github - [https://github.com/atlassian/react-beautiful-dnd](https://github.com/atlassian/react-beautiful-dnd)

React Drag and Drop Tutorial - [https://www.youtube.com/watch?v=uEVHJf30bWI](https://www.youtube.com/watch?v=uEVHJf30bWI)
