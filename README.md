# 📚 Actividad VI - Documentación Técnica

## 🚀 Descripción General
Aplicación web moderna desarrollada con **React** que implementa un sistema de gestión de tareas con interfaz responsive, manejo de estado global y integración con APIs.

## 🏗️ Arquitectura del Proyecto
actividad-VI/
├── public/
│ ├── index.html
│ └── manifest.json
├── src/
│ ├── components/ # Componentes reutilizables
│ ├── pages/ # Páginas principales
│ ├── hooks/ # Custom hooks
│ ├── context/ # Context API para estado global
│ ├── services/ # Llamadas a APIs
│ ├── utils/ # Utilidades y helpers
│ ├── styles/ # Archivos CSS/SCSS
│ ├── App.js # Componente principal
│ └── index.js # Punto de entrada
├── package.json
└── README.md

text

## ⚙️ Configuración Rápida

### 1. Instalación de Dependencias
```bash ```
npm install
2. Variables de Entorno
Crear archivo .env:

env
REACT_APP_API_URL=http://localhost:3000/api
REACT_APP_ENV=development
REACT_APP_VERSION=1.0.0
3. Ejecución de la Aplicación
bash
# Desarrollo
npm start

# Build para producción
npm run build

# Tests
npm test
🎯 Componentes Principales
1. Componente App (Principal)
jsx
import React from 'react';
import { TaskProvider } from './context/TaskContext';
import TaskManager from './components/TaskManager';

function App() {
  return (
    <TaskProvider>
      <div className="App">
        <TaskManager />
      </div>
    </TaskProvider>
  );
}

export default App;
## 2. Gestión de Estado con Context API
jsx
// src/context/TaskContext.js
import React, { createContext, useContext, useReducer } from 'react';

const TaskContext = createContext();

const taskReducer = (state, action) => {
  switch (action.type) {
    case 'ADD_TASK':
      return {
        ...state,
        tasks: [...state.tasks, action.payload]
      };
    case 'DELETE_TASK':
      return {
        ...state,
        tasks: state.tasks.filter(task => task.id !== action.payload)
      };
    default:
      return state;
  }
};

export const TaskProvider = ({ children }) => {
  const [state, dispatch] = useReducer(taskReducer, { tasks: [] });

  return (
    <TaskContext.Provider value={{ state, dispatch }}>
      {children}
    </TaskContext.Provider>
  );
};

export const useTask = () => useContext(TaskContext);
📡 Servicios y APIs
## 1. Servicio de Tareas
javascript
// src/services/taskService.js
import axios from 'axios';

const API_URL = process.env.REACT_APP_API_URL;

export const taskService = {
  // Obtener todas las tareas
  getTasks: async () => {
    try {
      const response = await axios.get(`${API_URL}/tasks`);
      return response.data;
    } catch (error) {
      throw new Error('Error al obtener tareas');
    }
  },

  // Crear nueva tarea
  createTask: async (taskData) => {
    try {
      const response = await axios.post(`${API_URL}/tasks`, taskData);
      return response.data;
    } catch (error) {
      throw new Error('Error al crear tarea');
    }
  },

  // Actualizar tarea
  updateTask: async (id, taskData) => {
    try {
      const response = await axios.put(`${API_URL}/tasks/${id}`, taskData);
      return response.data;
    } catch (error) {
      throw new Error('Error al actualizar tarea');
    }
  },

  // Eliminar tarea
  deleteTask: async (id) => {
    try {
      await axios.delete(`${API_URL}/tasks/${id}`);
      return id;
    } catch (error) {
      throw new Error('Error al eliminar tarea');
    }
  }
};
🎨 Estilos y UI
 ## 1. Estructura de Estilos
css
/* src/styles/App.css */
.App {
  text-align: center;
  background-color: #f5f5f5;
  min-height: 100vh;
}

.task-card {
  background: white;
  border-radius: 8px;
  padding: 16px;
  margin: 10px 0;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.task-completed {
  text-decoration: line-through;
  opacity: 0.7;
}
🔧 Hooks Personalizados
 ## 1. Hook para Gestión de Tareas
javascript
// src/hooks/useTasks.js
import { useState, useEffect } from 'react';
import { taskService } from '../services/taskService';

export const useTasks = () => {
  const [tasks, setTasks] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const loadTasks = async () => {
    setLoading(true);
    try {
      const tasksData = await taskService.getTasks();
      setTasks(tasksData);
      setError(null);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  const addTask = async (taskData) => {
    try {
      const newTask = await taskService.createTask(taskData);
      setTasks(prev => [...prev, newTask]);
    } catch (err) {
      setError(err.message);
    }
  };

  useEffect(() => {
    loadTasks();
  }, []);

  return { tasks, loading, error, addTask, loadTasks };
};
📱 Páginas y Routing
1. Configuración de Rutas
jsx
// src/App.js con React Router
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import Tasks from './pages/Tasks';
import About from './pages/About';

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/tasks" element={<Tasks />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Router>
  );
}
## 🧪 Testing
1. Configuración de Tests
javascript
// src/components/__tests__/TaskManager.test.js
import { render, screen, fireEvent } from '@testing-library/react';
import TaskManager from '../TaskManager';

test('renderiza el gestor de tareas correctamente', () => {
  render(<TaskManager />);
  expect(screen.getByText('Gestor de Tareas')).toBeInTheDocument();
});

test('permite agregar nueva tarea', () => {
  render(<TaskManager />);
  const input = screen.getByPlaceholderText('Nueva tarea');
  fireEvent.change(input, { target: { value: 'Tarea de prueba' } });
  fireEvent.click(screen.getByText('Agregar'));
  expect(screen.getByText('Tarea de prueba')).toBeInTheDocument();
});
2. Ejecución de Tests
bash
# Ejecutar tests
npm test

# Tests con cobertura
npm test -- --coverage

Tests en modo watch
npm test -- --watch
## 🚀 Build y Despliegue
1. Build de Producción
bash
# Crear build optimizado
npm run build

# Servir build localmente
npx serve -s build
2. Variables de Entorno Producción
env
REACT_APP_API_URL=https://api.tudominio.com
REACT_APP_ENV=production
REACT_APP_VERSION=1.0.0
📦 Scripts Disponibles
Script	Comando	Descripción
start	npm start	Servidor de desarrollo
build	npm run build	Build para producción
test	npm test	Ejecutar tests
eject	npm run eject	Ejectar configuración
🎯 Características Principales
⚛️ React 18 con hooks modernos

🎨 UI Responsive y accesible

🔄 Estado Global con Context API

📡 Integración con APIs REST

🧪 Suite de Tests completa

🚀 Optimizado para producción

📱 PWA ready

🎯 TypeScript opcional

🔧 Estructura de Componentes
text
components/
├── common/
│   ├── Button/
│   ├── Input/
│   └── Modal/
├── tasks/
│   ├── TaskList/
│   ├── TaskItem/
│   └── TaskForm/
└── layout/
    ├── Header/
    ├── Footer/
    └── Navigation/
