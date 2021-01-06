`LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 在 `MainLayout` 组件 (`Shared/MainLayout.razor`) 中呈现并管理以下行为：

* 对于经过身份验证的用户：
  * 显示当前用户名。
  * 提供用于注销应用的按钮。
* 对于匿名用户，提供登录选项。

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
