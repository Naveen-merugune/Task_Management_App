**Task Management App**

**Project Overview**  
This project is a **Task Management Application** that uses **Directus** for the backend and **Svelte** with **Flowbite** for the frontend. The application allows users to manage tasks with **CRUD** (Create, Read, Update, Delete) operations. Below are the detailed instructions on how to set up and run the project.

**Prerequisites**

**Backend**
1. **Docker**: Ensure that Docker is installed on your system.
   - Install **Docker Desktop** (Includes Docker Engine and Docker Compose).
   - Enable **WSL 2** if you are on Windows.
2. **Git**: Make sure Git is installed to clone the repository.

**Frontend**
1. **Node.js**: Install **Node.js**, which includes **npm** (Node Package Manager).
2. **pnpm**: An alternative package manager to **npm**, preferred for faster installations.

**Setup Instructions**

**Backend Setup**

**Clone the Repository**:  
`git clone https://github.com/Naveen-merugune/Task_Management_App.git`  
`cd Task_Management_App/task_manager_backend/Directus`

1. **Run Docker Compose**:  
   `docker-compose up -d`
   - This command will start Docker containers for **MySQL** and **Directus**, which will be accessible at **http://localhost:8055**.

2. **Access Directus**:
   - Go to **http://localhost:8055** in your browser.
   - Log in using the admin credentials specified in the `docker-compose.yml` file (**ADMIN_EMAIL** and **ADMIN_PASSWORD**).
   - After logging in, create a collection called **Tasks** with fields: **Title**, **Description**, **Status**, and **Due Date**.

**Frontend Setup**

**Navigate to Frontend Directory**:  
`cd task_manager_frontend`

1. **Install Node Modules**:  
   `pnpm install`
   - This command installs all the necessary dependencies listed in the **package.json**.

2. **Run the Development Server**:  
   `npm run dev`
   - The frontend application will start on **http://localhost:5173**.

3. **Access the Application**:
   - Go to **http://localhost:5173**.
   - Log in using the **Directus admin credentials**.
   - You will see the **Task Management App** with options to **Add, Get, Update**, and **Delete tasks**.

**API Endpoints**

The following **API endpoints** are used for integration between the frontend and backend:
- **Login**: `/auth/login`
- **Create Task**: `/items/Tasks`
- **Read Tasks**: `/items/Tasks`
- **Update Task**: `/items/Tasks/:id`
- **Delete Task**: `/items/Tasks/:id`

**Packages Installed for the Frontend**

The following commands were used to set up the frontend project:

- **Install pnpm**:  
  `npm install -g pnpm`

- **Create Svelte Project**:  
  `pnpm create svelte@latest task_manager_frontend`  
  `cd task_manager_frontend`

- **Install Flowbite for Svelte**:  
  `pnpm install flowbite-svelte`

- **Install Tailwind CSS with PostCSS and Autoprefixer**:  
  `pnpm install -D tailwindcss postcss autoprefixer`  
  `npx tailwindcss init -p`

**Setting Up a New Project**

If you are setting up a new project from scratch, follow these steps:

**Initialize the Project**:  
`pnpm create svelte@latest new_project_name`  
`cd new_project_name`

**Install Required Dependencies**:  
`pnpm install flowbite-svelte`  
`pnpm install -D tailwindcss postcss autoprefixer`  
`npx tailwindcss init -p`

**Configure Tailwind CSS**:
- Update the `tailwind.config.cjs` file to include **Flowbite** and your **Svelte files**.

**Start Development Server**:  
`npm run dev`
