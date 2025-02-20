# Blazor Puzzle #41

## Show Me The Email!

YouTube Video: https://youtu.be/

Blazor Puzzle Home Page: https://blazorpuzzle.com

### The Challenge:

This is a .NET 8 Blazor Web App with Global Server Interactivity.

The goal is to retrieve the logged-in user's email address in the NavMenu.

To do this, we copied code from Components\Account\Pages\Manage\Index.razor

After creating the initial auth database, and registering as a user, uncomment the code in NavMenu OnInitializedAsync()

Run the app and log in, then go to the Manage page to see the user's email address.

The app will crash with the error "A second operation was started on this context instance before a previous operation completed. This is usually caused by different threads concurrently using the same instance of DbContext."

How can we fix this?

### The Solution:

Rather than using the lower-level UserManager to retrieve the user identity, we should be using this a cascading `Task<AuthenticationState>`. 

The original solution was copied from a page that runs with no interactivity. No interactivity = no state. Using that code caused the error.

We modified the code to use this more "Blazory" method:

```c#
[CascadingParameter]
private Task<AuthenticationState> authenticationStateTask { get; set; } = default!;

protected override async Task OnInitializedAsync()
{
    currentUrl = NavigationManager.ToBaseRelativePath(NavigationManager.Uri);
    NavigationManager.LocationChanged += OnLocationChanged;

    var user = authenticationStateTask.Result.User.Identity;
    UserEmail = user?.Name;
}
```

Boom!