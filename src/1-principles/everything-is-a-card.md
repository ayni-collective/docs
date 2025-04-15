# **Principle 3: The "Everything is a Card" Paradigm**

**"A unified interface model where all components—apps, widgets, and content—exist as autonomous, interactive cards following declarative rules and physical interaction metaphors."**

---

## **1. Core Architecture**

### **Card Anatomy (Rust Implementation)**

```rust
#[derive(State)]
pub struct Card {
    pub id: Uuid,
    pub content: CardContent,  // Enum: App, Media, Widget, Notification
    pub behavior: CardBehavior, // Drag, Flip, Stack permissions
    #[observed]
    pub state: CardState,      // Focused/Minimized/Active
    #[observed]
    pub style: CardStyle,      // Inherits system theme + overrides
}
```

### **Key Properties**

- **Atomic**: Each card contains its own logic and presentation
- **Context-Aware**: Adapts behavior based on container/location
- **Physical Metaphors**: Flip, stack, shuffle interactions

---

## **2. Container System (Decks)**

| Type      | Behavior              | Use Case                | Kanban Equivalent |
| --------- | --------------------- | ----------------------- | ----------------- |
| **Stack** | Manual Z-ordering     | Window management       | N/A               |
| **Grid**  | Automated tiling      | Workspaces              | Swimlanes         |
| **Flow**  | Directional scrolling | App docks/notifications | Backlog           |
| **Board** | Column-based grouping | Task management         | Kanban Board      |

```rust
struct KanbanBoard {
    lanes: HashMap<LaneId, Lane>,  // "To Do", "In Progress", "Done"
    #[observed]
    cards: Vec<Card>,
    sorting: LaneRules,  // Auto-sort by priority/tags/deadline
}
```

---

## **3. Physical Interaction Model**

### **Card Gestures**

| Physical Action | Digital Implementation          | UX Purpose               |
| --------------- | ------------------------------- | ------------------------ |
| **Deal**        | Distribute cards across decks   | Workspace initialization |
| **Shuffle**     | Algorithmic reorganization      | Context switching        |
| **Flip**        | Show back panel (settings/logs) | Quick access             |
| **Stack**       | Temporary group creation        | Task batching            |
| **Fan**         | Peek at overlapping cards       | Content preview          |

### **Wayland Protocol Extension**

```xml
<interface name="zcard_gesture_v1">
    <method name="FlipCard">
        <arg name="card_id" type="string"/>
    </method>
    <event name="StackRequest">
        <arg name="cards" type="array" content_type="string"/>
    </event>
</interface>
```

---

## **4. Adaptive Layout Engine**

### **Declarative Rules**

```rust
fn arrange_cards(layout: LayoutType, cards: &[Card]) -> Vec<CardGroup> {
    match layout {
        LayoutType::Kanban => group_by_lanes(cards),
        LayoutType::Grid => {
            let grid = GridLayout::new()
                .with_columns(auto_detect_columns())
                .with_gutter(16);
            grid.arrange(cards)
        }
        // Other layouts...
    }
}
```

### **Performance Optimization**

- **Partial Updates**: Only reflow affected card groups
- **GPU-Accelerated**: Shared shaders for common card types
- **Lazy Loading**: Offscreen cards remain suspended

---

## **5. Theming System Integration**

### **Priority-Based Styling**

```ron
CardThemeProfile(
    default: ("daylight", 6500K),
    exceptions: [
        ("Video", (night: 3500K, brightness: -20%)),
        ("Critical", (accent: "#ff3e00", blink: 2Hz))
    ],
    containers: [
        ("Kanban", (lane_borders: true)),
        ("Grid", (drop_shadows: false))
    ]
)
```

### **State-Aware Appearance**

```glsl
// Card fragment shader
uniform int uCardState; // Focused/Minimized/Default

vec4 render_card() {
    if (uCardState == FOCUSED) {
        return apply_highlight(rgba);
    }
    // Default rendering...
}
```

---

## **6. Advantages Over Traditional Models**

| Aspect            | Card Paradigm             | Traditional Windows      |
| ----------------- | ------------------------- | ------------------------ |
| **Consistency**   | Unified interaction model | Varies by app/widget     |
| **Flexibility**   | Dynamic reorganization    | Manual window management |
| **Performance**   | Isolated card rendering   | Global repaints          |
| **Accessibility** | Predictable gestures      | Inconsistent controls    |

---

## **Next Implementation Steps**

1. **Core Protocols**

   - Wayland extensions for card drag/drop
   - DBus API for deck management

2. **Animation System**

   - Physics-based card movements
   - Transition curves for flipping/stacking

3. **Developer Tools**
   - Card debug overlay
   - Layout boundary visualizer

Would you like to prioritize any of these areas for deeper technical exploration?
