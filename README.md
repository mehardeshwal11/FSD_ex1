Experiment: Develop front-end applications using React’s state management, UI libraries, and performance optimization techniques.
Description: 
This project demonstrates the development of a modern front-end application using React with a strong focus on state management, UI libraries, performance optimization, and Progressive Web App (PWA) capabilities.
It showcases both Context API and Redux Toolkit for state handling, implements custom React hooks, and integrates popular UI libraries for scalable and responsive design.
Objectives:
a. Implement global state using Context API and optimize performance using memoization.
b. Manage advanced state using Redux Toolkit, including slices and selectors.
c. Build UI using Material UI, Ant Design, Styled Components, and convert into a PWA.
d. Implement a custom React hook (useThemeSwitcher) to toggle themes with optimized re-rendering.

react-project/
│── .gitignore
│── eslint.config.js
│── index.html
│── package.json
│── package-lock.json
│── README.md
│── vite.config.js
│
├── public/
│
└── src/
    │── components/
    │   ├── Navbar.jsx
    │   ├── ThemeToggle.jsx
    │
    │── pages/
    │   ├── Home.jsx
    │   └── About.jsx
    │
    │── hooks/
    │   └── useThemeSwitcher.js
    │
    │── context/
    │   └── ThemeContext.jsx
    │
    │── redux/
    │   ├── slices/
    │   │   └── themeSlice.js
    │   └── store.js
    │
    │── styles/
    │   ├── theme.js
    │   └── GlobalStyles.js
    │
    │── App.jsx
    │── main.jsx
