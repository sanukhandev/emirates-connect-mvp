# Emirates Connect Design System

## 1. Product visual direction
Emirates Connect should feel credible enough for established UAE business owners while remaining contemporary for founders and entrepreneurs. The UI is **modern, simple and Bento-inspired**: structured cards, strong hierarchy, generous whitespace, restrained motion and minimal decoration.

The design system is shared by Angular web and Flutter mobile. Platform-native interaction patterns are allowed, but the brand must remain visually consistent.

## 2. Brand foundation

### Typography
**Ubuntu is the only product typeface.** Use Google Font Ubuntu weights deliberately:
- 400 Regular — body copy and supporting labels.
- 500 Medium — controls, metadata emphasis and navigation.
- 700 Bold — headings and high-emphasis statistics.

Avoid adding secondary product fonts. System fonts may appear only as emergency fallbacks while Ubuntu loads.

Recommended hierarchy:
| Role | Size | Weight | Line height |
|---|---:|---:|---:|
| Display | 48px | 700 | 1.05 |
| H1 | 36px | 700 | 1.15 |
| H2 | 30px | 700 | 1.2 |
| H3 | 24px | 700 | 1.25 |
| H4 | 20px | 500/700 | 1.3 |
| Body lg | 18px | 400 | 1.55 |
| Body | 16px | 400 | 1.55 |
| Small | 14px | 400/500 | 1.45 |
| Caption | 12px | 500 | 1.4 |

Scale down display/H1 sizes on compact mobile widths.

### Core palette
| Token | Value | Intended use |
|---|---|---|
| `brand-primary` | `#9470F8` / `rgb(148 112 248)` | Primary CTA, active state, focus/brand accent |
| `brand-primary-hover` | `#835EEA` | Primary interactive hover |
| `brand-primary-soft` | `#EEE8FF` | Selected/brand-tinted surfaces |
| `canvas` | `#F8F7FA` | Main app background |
| `surface-card` | `#FFFFFF` | Cards, menus, panels |
| `surface-muted` | `#F1F0F4` | Secondary containers |
| `border-subtle` | `#E4E1EA` | Default dividers/borders |
| `content-primary` | `#27242D` | Main text |
| `content-secondary` | `#6F6A78` | Secondary text |
| `content-muted` | `#97919F` | Tertiary metadata |

Semantic success/warning/error colors may be introduced for status communication; they must not compete with the purple brand color and must pass WCAG AA contrast.

## 3. Spacing, radius and elevation
Use a 4px base grid. Preferred spacing steps: `4, 8, 12, 16, 20, 24, 32, 40, 48, 64`.

Recommended radii:
- Controls: 12px
- Standard cards: 16px
- Feature/Bento cards: 20px
- Large hero/media cards: 24px
- Pills/badges: fully rounded

Elevation must remain subtle. Prefer border + low elevation over heavy shadows. Do not use floating-card shadows everywhere.

## 4. Bento grid system
A Bento layout is a responsive information-composition system, not a collection of random card sizes.

Rules:
1. Every card has one clear purpose.
2. Priority determines span; visual novelty does not.
3. Desktop may use 12-column composition; feature modules can span 3/4/6/8/12 columns.
4. Tablet reduces span complexity.
5. Mobile collapses to one primary column unless a 2-column compact metric grid remains readable.
6. Keep aligned gutters and consistent card padding.
7. Avoid cards nested more than one level deep.
8. Dense social content such as feeds may use a stable reading column while secondary discovery modules occupy adjacent Bento cards on wide screens.

Suggested web shell:
```text
Desktop >= 1280
┌────────────┬──────────────────────────┬──────────────┐
│ Navigation │ Main feed/content        │ Discovery    │
│ 240-280px  │ minmax(0, 1fr)           │ 300-360px    │
└────────────┴──────────────────────────┴──────────────┘

Tablet
┌───────────┬────────────────────────────┐
│ Compact   │ Main content               │
│ nav       │                            │
└───────────┴────────────────────────────┘

Mobile
┌────────────────────────────────────────┐
│ Top bar                                │
├────────────────────────────────────────┤
│ Single-column content/cards            │
├────────────────────────────────────────┤
│ Bottom navigation                      │
└────────────────────────────────────────┘
```

## 5. Core UI primitives
Create reusable primitives before duplicating patterns:
- Button / IconButton
- AppShell / SideNav / BottomNav
- BentoGrid / BentoCard
- Card / MediaCard
- Avatar / AvatarGroup
- Badge / VerificationBadge / StatusBadge
- Input / Textarea / Select / Search
- Tabs / SegmentedControl
- Dropdown / Menu
- Dialog / Sheet / Drawer
- Toast / InlineAlert
- Skeleton / LoadingState
- EmptyState / ErrorState
- PostCard / CommentItem / Composer
- BusinessCard
- ReelCard / ReelPlayer controls

## 6. Interaction
- Minimum pointer target: 44x44px where practical.
- Focus indicators must be obvious and use the brand focus token with sufficient contrast.
- Do not communicate status using color alone.
- Hover is enhancement only; all functionality must work on touch.
- Keep transitions generally around 150–250ms.
- Respect `prefers-reduced-motion`.
- Skeletons should approximate final layout to reduce visual shift.

## 7. Angular + Tailwind implementation
Tailwind CSS is mandatory for the Angular web application.

### CSS token source
Define semantic variables centrally, for example:
```css
:root {
  --ec-brand-primary: 148 112 248;
  --ec-brand-primary-hover: 131 94 234;
  --ec-brand-primary-soft: 238 232 255;
  --ec-canvas: 248 247 250;
  --ec-surface-card: 255 255 255;
  --ec-surface-muted: 241 240 244;
  --ec-border-subtle: 228 225 234;
  --ec-content-primary: 39 36 45;
  --ec-content-secondary: 111 106 120;
  --ec-content-muted: 151 145 159;
  --ec-radius-control: 0.75rem;
  --ec-radius-card: 1rem;
  --ec-radius-bento: 1.25rem;
}
```

Expose these through the Tailwind theme mechanism appropriate to the installed Tailwind release. Do not scatter raw color values throughout Angular templates.

Preferred semantic utility intent:
```text
bg-brand-primary
hover:bg-brand-primary-hover
bg-brand-primary-soft
bg-app-canvas
bg-surface-card
bg-surface-muted
border-border-subtle
text-content-primary
text-content-secondary
text-content-muted
font-sans   -> Ubuntu
rounded-card
rounded-bento
```

### Angular font loading
Load Ubuntu once at application level. For production performance, prefer self-hosted Google Font files or the approved organization font-delivery policy; do not import multiple fonts per component.

### Tailwind usage rules
- Mobile-first responsive utilities.
- Use `@layer`/theme primitives only for deliberate shared abstractions.
- Avoid arbitrary values when a token exists.
- Keep long repeated class sets behind reusable Angular components or controlled class helpers.
- Never use `!important` as a normal styling strategy.
- Avoid dynamic class-name construction that Tailwind cannot statically discover.

## 8. Flutter implementation
Map the same semantics to Flutter:
```text
ColorScheme.primary        -> #9470F8
scaffold background        -> #F8F7FA
card/surface               -> #FFFFFF
secondary surface          -> #F1F0F4
primary text               -> #27242D
secondary text             -> #6F6A78
```

Use an application theme plus constants/extensions for spacing, radii and semantic surface colors. Ubuntu remains the only product font.

## 9. Accessibility
Target WCAG 2.2 AA for applicable web surfaces:
- contrast-compliant text and controls,
- keyboard operability,
- meaningful focus order,
- visible focus,
- semantic landmarks/headings,
- form labels and errors,
- captions/transcripts where video content requires them,
- reduced-motion support,
- meaningful alt text for informative images.

## 10. Design anti-patterns
Do not:
- imitate LinkedIn visually,
- overuse purple backgrounds,
- use pure-white pages with no visual hierarchy,
- use gradients as the default brand treatment,
- create inconsistent card radii,
- introduce a new color per feature,
- add a different font for marketing sections,
- create dense dashboards with every card competing for attention,
- hide primary actions behind hover-only behavior.
