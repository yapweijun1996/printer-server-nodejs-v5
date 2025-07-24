# Project Context: Node.js Print Server

This document provides a high-level overview of the Node.js Print Server project, its architecture, and key components. Its purpose is to help developers quickly understand the codebase and make contributions.

---

## 1. Project Goal

The primary goal of this project is to provide a reliable, high-quality printing solution for web applications. It bridges the gap between web front-ends and physical printers by offering a server-side API that can accept print jobs, render HTML into high-fidelity PDFs, and send them to a specified printer.

---

## 2. Architecture Overview

The project has been refactored into a modern, modular, and robust structure.

-   **Project Root**: The `package.json` and `node_modules` are now at the project root. All commands (`npm install`, `npm start`) should be run from here.
-   **Server (`/Server`)**: Contains all the back-end source code, organized into a modular structure.
-   **Public (`/public`)**: Contains a simple HTML, CSS, and JavaScript user interface for easy testing of the server's capabilities.

### Key Technologies

-   **Backend**: Node.js, Express.js
-   **PDF Generation**: Puppeteer (for high-quality, server-side rendering)
-   **Printing**: `pdf-to-printer` (a cross-platform printing library)
-   **Process Management**: PM2 (for production deployment, configured in `Server/ecosystem.config.cjs`)
-   **Logging**: Winston (for structured, file-based logging, configured in `Server/config/logger.js`)

---

## 3. File Structure

The repository is now organized as follows:

```
.
├── public/
│   ├── index.html      # Simple UI for testing
│   ├── style.css       # Styles for the UI
│   └── app.js          # Client-side logic for the UI
├── Server/
│   ├── config/         # For configuration files (e.g., logger)
│   ├── controllers/    # Handles the logic for each API route
│   ├── middleware/     # Custom middleware (e.g., error handling)
│   ├── routes/         # Defines the API routes
│   ├── logs/           # Directory for log files (created automatically)
│   ├── index.js        # The main server entry point
│   ├── .env            # Environment variables (port, etc.)
│   └── ecosystem.config.cjs # PM2 configuration
├── .gitignore
├── context.md          # This file
├── package.json        # Project dependencies and scripts (ROOT)
└── Readme.md           # Main project documentation
```

---

## 4. Core Components & Logic

### Server (`/Server`)

The server logic is now split into multiple directories for better maintainability:

1.  **`index.js`**: The main entry point. It initializes the Express app, sets up middleware, loads routes, serves the `public` directory, and starts the server. It also contains global error handlers to log any uncaught exceptions.
2.  **`config/logger.js`**: Configures the Winston logger to write logs to files within the `Server/logs` directory.
3.  **`routes/print.routes.js`**: Defines all the API endpoints (`/api/printers`, `/api/print-html`, etc.) and links them to the appropriate controller functions.
4.  **`controllers/print.controller.js`**: Contains the core business logic for each API endpoint. This is where Puppeteer is used, files are written, and print jobs are sent.
5.  **`middleware/errorHandler.js`**: A centralized error-handling middleware that ensures all API errors are sent back in a consistent JSON format.

### PM2 (`ecosystem.config.cjs`)

The `ecosystem.config.cjs` file in the `Server` directory configures PM2 to run the server. It's set up to:
- Run the server from the project root to ensure `node_modules` are found.
- Point to the correct entry script (`./Server/index.js`).
- Redirect all `stdout` and `stderr` to log files in `Server/logs/` for easy debugging.
- Run in `fork` mode for stability.

---

## 5. Setup and Management

This project can be set up and managed manually or via a convenience script on Windows.

### Windows Quick Management (`manage.bat`)

For Windows users, the `manage.bat` script in the project root is the recommended way to manage the server. It automates all common tasks. **Right-click `manage.bat` and "Run as administrator"** to access all features.

The script provides the following options:
-   **[1] Install Dependencies**: Automatically runs `npm install` and installs the `pm2` process manager globally. This is the first step for any new setup.
-   **[2] Start Server (Production)**: Starts the server using PM2, making it a persistent background service.
-   **[3] Stop Server**: Stops the running PM2 service.
-   **[4] Restart Server**: Restarts the PM2 service.
-   **[5] View Server Status and Logs**: Shows the PM2 process list and any console output.
-   **[6] Enable Auto-Startup on Reboot**: (Admin) Configures the server to start automatically when the computer reboots.
-   **[7] Disable Auto-Startup on Reboot**: (Admin) Removes the auto-startup configuration.
-   **[X] NUKE PM2 FROM ORBIT**: (Admin) A recovery tool that completely uninstalls PM2, kills all related processes, and clears all configurations. This is useful for fixing a corrupted PM2 installation and starting fresh.

### Manual Development Workflow (All Platforms)

1.  **Install dependencies**: From the **project root**, run `npm install`.
2.  **Start the server for development**: From the **project root**, run `npm start`. This uses `node` to run the server, and logs will appear directly in your console.
3.  **Start the server for production**: From the **project root**, run `npm run start:prod`. This uses `pm2` to run the server as a background service, as defined in `Server/ecosystem.config.cjs`.
4.  **Test with the UI**: Open `http://localhost:3000` in a web browser.
5.  **Make changes**: Modify the relevant files in the `Server` directory.
6.  **Restart the server**:
    - For development, stop the server (Ctrl+C) and restart with `npm start`.
    - For production, run `npm run restart:prod` or use the `pm2 restart print-server` command.