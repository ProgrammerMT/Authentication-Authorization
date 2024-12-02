# ASP.NET Core MVC Project with Authentication and Cookie Management

## Overview
This project is a simple ASP.NET Core MVC application with authentication and authorization using ASP.NET Identity. It features role-based access control, user registration, and cookie management for user sessions. The application includes specific configuration for managing cookies, ensuring secure access and controlled expiration.

Made for Web Development class @ Vocational School of Electrical Engineering and Electronics.

## Features
- **User Authentication**: Allows users to register, log in, and log out securely.
- **Role-Based Authorization**: Restricts access to specific areas of the application based on user roles (e.g., Admin).
- **Cookie Management**: Uses ASP.NET Identity to manage session cookies for logged-in users, including custom cookie expiration settings.
- **Admin Page**: Admin-specific page that is accessible only to users in the "Admin" role.

## Technologies Used
- **ASP.NET Core 8.0 MVC**
- **Entity Framework Core** for database operations
- **ASP.NET Identity** for authentication and authorization
- **SQL Server** as the database (LocalDB for development)

## Project Structure
- **Controllers**: Handles the main logic for authentication, authorization, and page routing.
- **Views**: Razor views for displaying UI to the users, including pages for registration, login, and the admin section.
- **_Layout.cshtml**: Contains the common layout for the application, including a dynamic navigation bar that displays different options depending on the user's authentication and role.

## Setup Instructions

### Prerequisites
- **.NET SDK 8.0**
- **Visual Studio 2022** or newer
- **SQL Server (LocalDB)**

### Steps to Run the Application
1. **Clone the Repository**:
   ```bash
   git clone <repository-url>
   cd <project-folder>
   ```
2. **Configure Database**:
   Update the `appsettings.json` file with your SQL Server connection string:
   ```json
   "ConnectionStrings": {
     "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=aspnet-YourAppName;Trusted_Connection=True;MultipleActiveResultSets=true"
   }
   ```
3. **Apply Migrations**:
   Run the following commands in the Package Manager Console to create the database:
   ```bash
   Add-Migration InitialCreate
   Update-Database
   ```
4. **Run the Application**:
   Press `F5` in Visual Studio or run the following command:
   ```bash
   dotnet run
   ```

### Default Roles Setup
The application includes roles like **Admin** and **User**. The `SeedData` class is used to create these roles and an initial admin user:
- **Email**: `admin@example.com`
- **Password**: `Admin@123`

To run the seeding process, it is executed when the application starts. You can modify or add new roles if needed.

## Cookie Configuration
- The application uses **session cookies** to maintain user authentication between requests.
- **Expire Time**: Cookies are set to expire after 30 minutes of inactivity. The expiration can be configured in the `Program.cs` file:
  ```csharp
  services.ConfigureApplicationCookie(options =>
  {
      options.ExpireTimeSpan = TimeSpan.FromMinutes(30);
      options.Cookie.HttpOnly = true;
      options.Cookie.IsEssential = true;
      options.SlidingExpiration = true;
  });
  ```
- **Session Cookie**: If the user closes the browser, the session cookie will be deleted automatically, unless you specify a persistent cookie.

## Admin Access Button in Layout
In `_Layout.cshtml`, the **Admin Page** button is shown only if the logged-in user has the "Admin" role. This is implemented using Razor syntax:

```csharp
@if (User.Identity.IsAuthenticated && User.IsInRole("Admin"))
{
    <li><a href="/Admin">Admin Page</a></li>
}
```
This ensures that only authorized users can see the link to the admin section.

## Logout and Cookie Deletion
- **Logout Process**: When a user logs out, all cookies related to their session are deleted:
  ```csharp
  public async Task<IActionResult> Logout()
  {
      await signInManager.SignOutAsync();
      return RedirectToAction("Index", "Home");
  }
  ```
- **Cookie Expiration**: Cookies expire based on inactivity (`ExpireTimeSpan`), ensuring that user sessions are automatically logged out if inactive for a defined period.

## Troubleshooting
- **Login Loop Issue**: If you encounter issues with being redirected back to the login page after logging in, ensure:
  - Cookies are enabled in your browser.
  - The `UseAuthentication()` and `UseAuthorization()` methods are correctly ordered in `Program.cs`.
  - The roles and user claims are properly configured.

