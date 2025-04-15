# Declarative Shell Architecture

**Core Principle**:  
_"The desktop interface is defined through reactive state transformations and declarative rules, enabling deterministic rendering and simplified maintenance."_

## 1. Core Architecture

### State Management System

```rust
#[derive(StateTree)]
pub struct ShellState {
    #[state(global)]
    pub theme: ThemeState,

    #[state(session)]
    pub workspaces: Vec<Workspace>,

    #[state(ephemeral)]
    pub input_focus: Option<WindowId>,
}
```

### Reactive Update System

```rust
state.observe(|changes| {
    compositor.update_surfaces(changes);
    iced_app.request_redraw();
});
```

## 2. Wayland Integration

### Protocol Support

```rust
// Declarative window definition
xdg_surface()
    .title(state.title)
    .size(state.preferred_size)
    .decorations(match state.decoration_mode {
        DecorationMode::Server => server_side_decorations(),
        DecorationMode::Client => client_side_decorations(),
    })
```

### Performance Pipeline

```mermaid
graph LR
    A[State Change] --> B[Change Detection]
    B --> C[Layout Recalculation]
    C --> D[GPU Command Generation]
    D --> E[Wayland Commit]
```

## 3. Component System

### Card Integration

[Related: Everything is a Card](./everything-is-a-card.md)

```rust
struct CardComponent {
    id: Uuid,
    content: CardContent,
    state: ComponentState,
    #[serde(skip)]
    renderer: CardRenderer,
}
```

## 4. Framework Integration

| Framework   | Purpose   | Integration Point  |
| ----------- | --------- | ------------------ |
| **Iced**    | Widget UI | Main view layer    |
| **Smithay** | Wayland   | Compositor backend |
| **Tokio**   | Async     | Event system       |

```rust
struct ShellFramework {
    iced: iced::Application,
    wayland: Arc<Mutex<SmithayCompositor>>,
    runtime: tokio::runtime::Runtime,
}
```

## 5. System Services

### DBus Integration

```rust
dbus_conn.add_match(
    MatchRule::new()
        .interface("org.freedesktop.portal.Settings")
        .member("SettingChanged")
)?;
```

### Security Model

```ron
sandbox_permissions {
    filesystem: "read-only:/usr/share/themes",
    network: "none",
    gpu: "limited:no-shaders",
}
```

## Advantages Over Traditional Shells

1. **Maintainability**:

   ```rust
   // Before (imperative)
   if dark_mode { set_dark_theme() } else { set_light_theme() }

   // After (declarative)
   theme.apply(state.ui_mode)
   ```

2. **Performance**:

   - Parallel state updates
   - Batched Wayland commits
   - Isolated component rendering

3. **Consistency**:
   - Unified theming through state
   - Deterministic rendering

## Cross-References

- [Wayland Integration](../architecture/wayland-integration.md)
- [Adaptive Theming](../principles/adaptive-theming.md)
- [Rust API](../api/rust-api.md)

## Roadmap

1. **Core Features**:

   - State persistence
   - Multi-monitor support

2. **Optimizations**:

   - GPU-accelerated compositing
   - Damage tracking

3. **Ecosystem**:
   - Developer tools
   - Documentation suite

This architecture combines Rust's safety guarantees with declarative UI patterns for a maintainable and performant shell implementation.
