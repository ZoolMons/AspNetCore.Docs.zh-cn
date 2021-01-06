`App` 组件 (`App.razor`) 类似于 Blazor Server 应用中的 `App` 组件：

* <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 组件管理向应用的其余部分公开 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>。
* <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 组件确保当前用户有权访问给定页面或以其他方式呈现 `RedirectToLogin` 组件。
* `RedirectToLogin` 组件管理将未经授权的用户重定向到登录页。

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!include[](../prefer-exact-matches.md)]
