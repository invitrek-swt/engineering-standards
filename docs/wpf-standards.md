# WPF Architecture Standards — Invitrek-swt

**Applies to:** All WPF/UIFramework projects  
**Last updated:** See git history

---

## 1. Reusable WPF Foundation Library

- Treat `Invitrek.Foundation.Presentation.Controls` as the primary reusable WPF foundation library.
- All Foundation Presentation libraries share a single XAML namespace: prefix `ifx`, CLR root namespace `Invitrek.Foundation.Presentation.Controls`.
- Add assembly-level `XmlnsDefinition`/`XmlnsPrefix` attributes so controls can be used with a stable XAML namespace and prefix across projects.
- Prefer built-in WPF primitive controls; extend via `DependencyProperties` and `RoutedEvents`.
- Control templates must use `TemplateBinding` and theme keys via `DynamicResource`.
- Runtime customization is via overriding theme resources only — not changing behaviors.
- Maintain a separate `ResourceDictionary` per individual custom control; index them via `MergedDictionaries` in `Themes/Generic.xaml`.

---

## 2. Custom Control Authoring (Mandatory)

- Custom controls shall contain **no business/domain logic**.
- Controls must remain general-purpose and reusable; domain-specific behavior belongs in domain layers (ViewModels/services/adapters).
- Expose dependency properties for all commonly themeable/styleable aspects and common layout properties (width, height, content alignment, margin, padding, etc.).
- For controls that host children, derive from `ItemsControl` to support MVVM (`ItemsSource`, `ItemTemplate`) and override item container generation (`IsItemItsOwnContainerOverride`, `GetContainerForItemOverride`) to provide a strongly typed, theme-aware container.

---

## 3. XAML and Resource Dictionary References

- In WPF XAML, always use **fully-qualified pack URIs** (including assembly name) when referencing resource dictionaries for reliable runtime resource loading across targets:

```xml
<ResourceDictionary Source="pack://application:,,,/Invitrek.Foundation.Presentation.Controls;component/Themes/Generic.xaml" />
```

---

## 4. Icon Usage

- Use **vector/geometry-based icons** everywhere instead of emoji or text glyphs.
- Theme icon fill via `SetResourceReference(Shape.FillProperty, ThemeKeys.SubtleTextBrushKey)` for theme-aware color.
- Standard icon size: 16×16, `Stretch=Uniform`.

---

## 5. Theming

- Theme the WPF UI to match Visual Studio light style as the default.
- Keep appearance controlled entirely through brushes so it can be restyled later without changing behavior.
- All dimension constants for docking/tool-window chrome: **24px title bar height**, **24×24 action buttons**.

---

## 6. Composable Layout Base

- Use `HeaderFooterContentControl` (derived from `HeaderedContentControl`) as a base for composing complex layouts:
  - `Header` → hosts `CommandBar`, `MenuBar`, etc.
  - `Footer` → hosts `StatusBar`.
  - `Content` → hosts arbitrary content.
- Implement footer-related dependency properties analogous to `HeaderedContentControl`'s `Header`/`HeaderTemplate`/`HeaderTemplateSelector`/`HeaderStringFormat` patterns.

---

## 7. DockingLayout

- Complete core features for `DockingLayout` before UI integration.
- Provide save/restore layout abstraction with a default implementation persisting to the host executable's `AppData` folder.
- Use a `HeaderedContentControl`-based container for docked windows (holds title and state: position, auto-hide, close).

---

## 8. XML Namespace Guidelines

- XML namespace categories can grow as the controls library grows.
- Avoid abstract/overly granular categories and deep nesting.
- Introduce new categories only when they reflect a clear area-of-use boundary and potential future assembly split.

---

## 9. UI Control Semantic Naming Conventions (Mandatory)

All custom UI control types shall use a **semantic role suffix** that communicates the type's behavioral category at a glance. These conventions apply to WPF and to every other UI platform the UIFramework targets.

### Suffix reference table

| Suffix | Behavioral role | Examples |
|--------|-----------------|---------|
| `*.Bar` | Horizontal or vertical strip hosting commands, status, or navigation items | `CommandBar`, `StatusBar`, `BreadcrumbBar`, `ToolBar` |
| `*.Menu` | Hierarchical or pop-up menu | `MainMenu`, `ContextMenu`, `DropDownMenu`, `SubMenu` |
| `*.Panel` | Named content zone that arranges children spatially | `SplitPanel`, `DockingPanel`, `SidebarPanel` |
| `*.Layout` | Layout control that manages positioning/arrangement of child controls or views | `FlyoutLayout`, `StackLayout`, `GridLayout` |
| `*.Canvas` | Free-form drawing or design surface | `DesignerCanvas`, `InkCanvas` |
| `*.Surface` | Interactive editing surface (use when `Canvas` semantics are too narrow) | `DesignSurface`, `HtmlSurface` |

---

## 10. WizardControl Standards

- Use three named `ControlTemplate` resources (`ClassicWizardTemplate`, `MetroWizardTemplate`, `ImmersiveWizardTemplate`) defined as keyed resources; swap via `Style.Triggers`.
- All three templates share the same `PART_` contract: `PART_BackButton`, `PART_NextButton`, `PART_FinishButton`, `PART_CancelButton`, `PART_StepContent`.
- `WizardStepChangingEventArgs` shape: `Previous` (nullable, null on first navigation), `CurrentPage` (publicly settable for redirect), `Direction` (`Forward`/`Back` only — no `Direct`), `Cancel`.
- `WizardControl.IsValid` reactivity: subscribe to `WizardStep.IsValidProperty` changes via `DependencyPropertyDescriptor.AddValueChanged`; call `CommandManager.InvalidateRequerySuggested()` on change.

---

## 11. ThemedWindow Flyout

- `ThemedWindow` embeds `FlyoutLayoutControl` as a named overlay in the content area grid.
- Three DPs on `ThemedWindow`: `FlyoutContent`, `IsFlyoutOpen` (TwoWayByDefault), `FlyoutDirection` (default Right).
- Set `IsHitTestVisible` to `false` when flyout is closed to prevent blocking content interaction.
