# **Principle 3: The "Everything is a Card" Paradigm**

**"The interface is built from self-contained cards—autonomous, modular units that represent apps, widgets, or content—organized in flexible containers with declarative rules."**

---

## **1. Core Philosophy: Cards as Atomic Units**

Every UI element is a **card** with:

- **Content** (apps, widgets, media, notifications).
- **Behavior** (draggable, resizable, stackable).
- **State** (active, minimized, focused).
- **Adaptive theming** (system-wide + per-card customization).

### **Rust Implementation (Base Card):**

```rust
#[derive(State)]
pub struct Card {
    pub id: Uuid,
    pub content: CardContent,  // App, widget, media, etc.
    pub behavior: CardBehavior,
    #[observed]
    pub state: CardState,      // Focus, minimized, etc.
    #[observed]
    pub style: CardStyle,      // Inherits system theme + overrides
}
```

---

## **2. Card Containers (Decks & Kanban Boards)**

**Decks** define how cards are grouped and laid out:

| Container Type | Purpose                         | Example Use Case                  |
| -------------- | ------------------------------- | --------------------------------- |
| **Stack**      | Overlapping cards (traditional) | Window management                 |
| **Grid**       | Tiled layout (minimal tiling)   | Workspace organization            |
| **Flow**       | Horizontal/vertical scrolling   | App docks or notifications        |
| **Kanban**     | Trello/Notion-style boards      | Task management, project tracking |

### **Kanban Board Implementation**

```rust
struct KanbanBoard {
    lanes: Vec<Lane>,  // e.g., "To Do", "In Progress", "Done"
    cards: Vec<Card>,
    sorting_rules: LaneRules,  // Auto-sort by priority/tags
}

impl KanbanBoard {
    fn update_card_position(&mut self, card_id: Uuid, new_lane: LaneId) {
        // Move card between lanes with animation
    }
}
```

---

## **3. Card Interaction Philosophy**

- **No traditional "windows"**—only cards in decks.
- **Unified gestures**: All cards support:
  - **Drag/drop** (between decks or lanes).
  - **Flip** (show backside for settings/details).
  - **Stack** (group related cards temporarily).
  - **Shuffle** (rearrange dynamically).

### **Example: Physical Card Metaphors**

| Gesture     | Digital Adaptation                              |
| ----------- | ----------------------------------------------- |
| **Deal**    | Distribute cards across workspaces              |
| **Shuffle** | Randomize card layouts (e.g., for multitasking) |
| **Fan**     | Peek at stacked cards                           |

---

## **4. Declarative Layout Rules**

Cards self-organize based on:

- **Container type** (Stack/Grid/Kanban).
- **Contextual relationships** (auto-grouping by project/tag).
- **User focus** (priority scaling for active cards).

```rust
// Declarative layout example for a Kanban lane
fn render_lane(lane: &Lane) -> impl RenderRule {
    FlowLayout::horizontal()
        .spacing(16)
        .children(lane.cards.iter().map(|card| {
            card.render()
                .with_behavior(BEHAVIOR_DRAGGABLE)
                .with_theme(lane.theme)
        }))
}
```

---

## **5. Theming & Adaptive Behavior**

Cards inherit system themes but can override:

```ron
// Per-card theming rules
CardTheme(
    default: ("daylight-neutral", 6500K),
    overrides: {
        "VideoCard": (night_temp: 3500K, dim: 20%),
        "TaskCard": (priority_color: "#ff3e00")
    }
)
```

---

## **6. Why This Works**

✅ **Unified interaction model**: No distinction between "apps" and "widgets."  
✅ **Extreme flexibility**: From tiling (Grid) to freeform (Kanban).  
✅ **Physicality-inspired UX**: Intuitive gestures (flip, stack, shuffle).  
✅ **Scalable**: Nested cards (e.g., tabs within apps) or decks within decks.

---

## **Next Steps**

Would you like:

1. **Wayland protocol extensions** for card drag/drop?
2. **Animation examples** (card flipping, lane transitions)?
3. **Integration with Cosmic-compatible tiling**?

This approach keeps your principles intact while adding Kanban/Trello-like flexibility!
