---
name: Nguyen Tri Phuong EMR & Outpatient Care
colors:
  surface: '#f5faff'
  surface-dim: '#c4ddf0'
  surface-bright: '#f5faff'
  surface-container-lowest: '#ffffff'
  surface-container-low: '#eaf5ff'
  surface-container: '#def0ff'
  surface-container-high: '#d2ecff'
  surface-container-highest: '#cde6f9'
  on-surface: '#031e2c'
  on-surface-variant: '#3f4850'
  inverse-surface: '#1b3342'
  inverse-on-surface: '#e4f3ff'
  outline: '#6f7881'
  outline-variant: '#bec8d1'
  surface-tint: '#006591'
  primary: '#00628d'
  on-primary: '#ffffff'
  primary-container: '#007cb1'
  on-primary-container: '#fcfcff'
  inverse-primary: '#89ceff'
  secondary: '#bc0002'
  on-secondary: '#ffffff'
  secondary-container: '#e2261a'
  on-secondary-container: '#fffbff'
  tertiary: '#176287'
  on-tertiary: '#ffffff'
  tertiary-container: '#397ba1'
  on-tertiary-container: '#fcfcff'
  error: '#ba1a1a'
  on-error: '#ffffff'
  error-container: '#ffdad6'
  on-error-container: '#93000a'
  primary-fixed: '#c9e6ff'
  primary-fixed-dim: '#89ceff'
  on-primary-fixed: '#001e2f'
  on-primary-fixed-variant: '#004c6e'
  secondary-fixed: '#ffdad5'
  secondary-fixed-dim: '#ffb4a8'
  on-secondary-fixed: '#410000'
  on-secondary-fixed-variant: '#930001'
  tertiary-fixed: '#c7e7ff'
  tertiary-fixed-dim: '#8fcef7'
  on-tertiary-fixed: '#001e2e'
  on-tertiary-fixed-variant: '#004c6c'
  background: '#f5faff'
  on-background: '#031e2c'
  surface-variant: '#cde6f9'
typography:
  headline-xl:
    fontFamily: Be Vietnam Pro
    fontSize: 36px
    fontWeight: '700'
    lineHeight: 44px
    letterSpacing: -0.02em
  headline-xl-mobile:
    fontFamily: Be Vietnam Pro
    fontSize: 28px
    fontWeight: '700'
    lineHeight: 36px
    letterSpacing: -0.01em
  headline-lg:
    fontFamily: Be Vietnam Pro
    fontSize: 28px
    fontWeight: '700'
    lineHeight: 36px
    letterSpacing: -0.01em
  headline-lg-mobile:
    fontFamily: Be Vietnam Pro
    fontSize: 24px
    fontWeight: '700'
    lineHeight: 32px
    letterSpacing: -0.01em
  headline-md:
    fontFamily: Be Vietnam Pro
    fontSize: 22px
    fontWeight: '600'
    lineHeight: 30px
  headline-sm:
    fontFamily: Be Vietnam Pro
    fontSize: 20px
    fontWeight: '600'
    lineHeight: 28px
  body-patient-primary:
    fontFamily: Be Vietnam Pro
    fontSize: 18px
    fontWeight: '400'
    lineHeight: 28px
  body-patient-medium:
    fontFamily: Be Vietnam Pro
    fontSize: 18px
    fontWeight: '500'
    lineHeight: 28px
  body-patient-bold:
    fontFamily: Be Vietnam Pro
    fontSize: 18px
    fontWeight: '700'
    lineHeight: 28px
  body-base:
    fontFamily: Be Vietnam Pro
    fontSize: 16px
    fontWeight: '400'
    lineHeight: 24px
  body-base-medium:
    fontFamily: Be Vietnam Pro
    fontSize: 16px
    fontWeight: '500'
    lineHeight: 24px
  button-text:
    fontFamily: Be Vietnam Pro
    fontSize: 18px
    fontWeight: '700'
    lineHeight: 24px
  caption:
    fontFamily: Be Vietnam Pro
    fontSize: 16px
    fontWeight: '400'
    lineHeight: 22px
  badge-label:
    fontFamily: Be Vietnam Pro
    fontSize: 14px
    fontWeight: '600'
    lineHeight: 18px
    letterSpacing: 0.02em
rounded:
  sm: 0.25rem
  DEFAULT: 0.5rem
  md: 0.75rem
  lg: 1rem
  xl: 1.5rem
  full: 9999px
spacing:
  gutter: 1rem
  gutter-md: 1.5rem
  gutter-lg: 2rem
  margin: 1rem
  margin-md: 2rem
  margin-lg: 3rem
  space-xs: 0.25rem
  space-sm: 0.5rem
  space-md: 1rem
  space-lg: 1.5rem
  space-xl: 2rem
  space-2xl: 3rem
---

## Brand & Style

This design system establishes a high-accessibility, dignified public healthcare interface for Bệnh viện Nguyễn Tri Phương. It balances institutional authority with compassionate clarity, designed specifically for outpatients, elderly demographics, and clinical practitioners.

### Core Tenets
- **Clarity Over Novelty:** Structural layouts, direct language, and explicit wayfinding prioritize patients managing stress or physical limitations.
- **Strict Accessibility:** Minimum font size of 16px across patient-facing views, high-contrast text rendering exceeding WCAG 2.2 AA (up to 15.8:1), and oversized touch targets (minimum 48×48px).
- **Clinical Restraint with Decisive Urgency:** Clean, cool surgical blues construct a calm clinical atmosphere, punctuated by a controlled, singular crimson hue reserved exclusively for high-intent conversion and life-critical actions.

## Colors

The palette is engineered to meet strict WCAG 2.2 AA contrast ratios, optimized for high legibility under daylight, dimmed wards, and across varying mobile display qualities.

### Typography & Foundations
- `ink-900` (`#0B2533`): Primary typography and deep focal anchors. Delivers a 15.8:1 contrast ratio against white surfaces.
- `ink-700` (`#46596A`): Secondary body copy, supportive metadata, and secondary label groups.
- `ink-500` (`#5E7080`): Tertiary captions, timestamps, and input placeholders. Strictly forbidden under 16px in patient views.
- `line-300` (`#D3DEE6`): Subtle card perimeters, horizontal dividers, and structural grouping borders.
- `line-500` (`#7B8E9D`): High-visibility form field borders, unselected checkboxes, and radio button rings.
- `bg-page` (`#F4F8FA`): Tinted hospital-grade background preventing glare and visual fatigue.
- `bg-surface` (`#FFFFFF`): Elevated cards, modals, sheets, and interactive containers.

### Brand & Clinical Blues
- `blue-500` (`#008DC9`): Brand primary, navigational iconography, selection rings, and interactive focus states.
- `blue-600` (`#006E9B`): Darkened interactive surface fill ensuring accessible white-text contrast.
- `blue-800` (`#00587C`): H1 and H2 display headers, footer structures, and saturated blue accent text on pale tints.
- `blue-100` (`#D6EDF8`): Selected state container fills, active calendar dates, and active category chips.
- `blue-50` (`#EAF6FB`): Contextual callouts, hero greeting banners, and patient summary panels.

### Action Red & System States
- **Primary Actions:**
  - `red-500` (`#E02519`): Singular primary conversion action ("Xác nhận đặt khám", "Thanh toán viện phí"). Strictly capped at one solid red button per screen.
  - `red-600` (`#BF1E14`): Hover state for red primary buttons.
  - `red-700` (`#9E170F`): Active/pressed state and text links on pale red backgrounds.
  - `red-50` (`#FDEEEC`): Background fill for validation errors and "Từ chối / Hủy hẹn" badges.
- **Success & Recovery:**
  - `green-700` (`#1B6E3A`) text on `green-50` (`#E7F5EC`) surface: Confirmed appointments, normal test results, and payment success.
- **Warnings & Medical Cautions:**
  - `amber-800` (`#7A4B00`) text on `amber-50` (`#FFF3D6`) surface: Drug allergy alerts, fasting requirements, and schedule reschedule notices.

## Typography

The typography leverages **Be Vietnam Pro** across all touchpoints, chosen for native diacritic rendering in Vietnamese, balanced proportions, and open counters.

### Dynamic Type Scaling (A, A+, A++)
Interfaces must adapt dynamically via user-selected scaling modes without breaking layouts, overlapping borders, or clipping labels:
- **A (Standard - 100%):** Standard base sizes as specified.
- **A+ (Enlarged - 115%):** Body text scales from 18px to 20.7px; baseline 16px scales to 18.4px.
- **A++ (Maximum Legibility - 130%):** Body text scales from 18px to 23.4px; baseline 16px scales to 20.8px.

### Hierarchy Guidelines
- **Elderly Patient Accessibility Rule:** No instructional text, step indicator, form label, or diagnostic detail in patient-facing screens may drop below `16px`. `14px` is reserved strictly for compact status badges (`badge-label`) and dense clinical desktop dashboards for hospital staff.
- **Headings:** Use `blue-800` (`#00587C`) for top-tier headings (`headline-xl`, `headline-lg`) to preserve visual comfort and hierarchy over stark black.

## Layout & Spacing

A fluid responsive grid guides responsive progression from single-column patient flows on mobile to multi-column EMR medical records on desktop workstations.

### Grid & Breakpoints
- **Mobile (< 768px):** 4-column fluid layout with `margin: 1rem` (16px) and `gutter: 1rem` (16px). Cards span 4 columns. Navigation sticks to the bottom viewport for thumb accessibility.
- **Tablet (768px - 1023px):** 8-column layout with `margin-md: 2rem` (32px) and `gutter-md: 1.5rem` (24px). Split views for appointment slot picking and preliminary summary.
- **Desktop (≥ 1024px):** 12-column layout with max-width `1280px`, `margin-lg: 3rem` (48px), and `gutter-lg: 2rem` (32px). Sidebars handle patient profiles and navigation, while primary canvases handle timeline and booking steps.

### Hit Targets & Spacing Rhythm
- **Patient Touch Zone:** Minimum interactive footprint of `48×48px` on mobile and tablet touchscreens.
- **Staff Touch Zone:** Minimum interactive footprint of `40×40px` on desktop EMR entry tables.
- Component-level spacing adheres strictly to 8px and 4px baseline increments.

## Elevation & Depth

To maintain high visual comfort for tired eyes, elevation relies on clean surface layering paired with soft, tinted ambient shadows rather than stark heavy dropshadows.

### Shadow Definitions
- **Shadow 1 (s1 - Rest State):** `0 1px 2px rgba(11, 37, 51, 0.08)`. Applied to surface cards, segmented tabs, and form control groups to lift them subtly from `bg-page`.
- **Shadow 2 (s2 - Overlay & Hover State):** `0 8px 24px rgba(11, 37, 51, 0.10)`. Applied to active dropdown menus, floating confirmation bars, bottom action sheets, and modal dialogues.

### Layering Model
- **Layer 0 (Base):** Canvas background tinted in `bg-page` (`#F4F8FA`).
- **Layer 1 (Card/Container):** Pure `bg-surface` (`#FFFFFF`) with 1px border of `line-300` (`#D3DEE6`) and `s1` shadow.
- **Layer 2 (Popovers/Sheets):** Elevated white surface using `s2` shadow without thick borders.

## Shapes

The design system employs a calibrated corner radius scale to communicate friendliness, clinical safety, and functional containment.

### Shape Mapping
- **Standard Controls (12px):** Buttons, form inputs, dropdown selectors, date pickers, and filter chips.
- **Cards & Data Modules (16px):** Medical summary cards, appointment panels, doctor bio cards, and EMR metric tiles.
- **Macro Enclosures (24px):** Hero section headers, modal dialogues, bottom checkout sheets, and notification snackbars.
- **Pill Badges (999px):** Status badges ("Chờ khám", "Đã thanh toán", "Có bảo hiểm BHYT"), queue tokens, and tag pills.

## Components

### Buttons
- **Primary Red Action Button:**
  - Purpose: High-intent conversions ("Xác nhận đặt khám", "Thanh toán"). Max 1 solid red button per view.
  - Sizing: Min height 52px (patient), text `18px/24px` bold, border-radius 12px.
  - Palette: Background `red-500`, text `#FFFFFF`. Hover: `red-600`. Active: `red-700`.
- **Secondary Blue Button:**
  - Sizing: Min height 48px, text `18px/24px` bold.
  - Palette: Background `blue-50`, text `blue-800`, border 1px solid `blue-500`. Hover: `blue-100`.
- **Tertiary / Back Button:**
  - Sizing: Min height 48px. Text `16px/24px` medium, color `ink-700`.

### Form Fields & Date Pickers
- **Input & Select Boxes:**
  - Sizing: Min height 52px (patient touch). Padding: 12px 16px. Border-radius: 12px.
  - Border: 1.5px solid `line-500` (`#7B8E9D`). Background: `bg-surface`.
  - Focus State: 2px outline of `blue-500` (`#008DC9`), background `bg-surface`.
  - Placeholder & Helpers: `ink-500` (min 16px).
  - Error State: 2px outline of `red-500`, background `red-50` with icon validation message in `red-700`.

### Checkboxes & Radio Buttons
- **Touch Target:** Strict 48×48px clickable wrapper. Visual control size: 24×24px.
- **States:** Border 2px solid `line-500`. Selected: Background `blue-500` with white tick/bullet.

### Chips & Slot Pickers (Time & Doctor Select)
- **Time Slot Chips:**
  - Min height 48px, horizontal padding 20px, radius 12px.
  - Inactive: Background `bg-surface`, border 1px solid `line-300`, text `ink-900`.
  - Selected: Background `blue-100`, border 2px solid `blue-500`, text `blue-800` (bold).

### Cards & Medical Summaries
- **Structure:** Background `bg-surface`, border 1px solid `line-300`, radius 16px, shadow `s1`.
- **Inner Padding:** 20px on mobile, 24px on desktop.
- **Dividers:** 1px solid `line-300`.

### Medical Status Badges (Pills)
- Shape: 999px radius, padding 6px 14px, typography `badge-label`.
- **Success:** `green-700` on `green-50`.
- **Allergy & Danger Alert:** `amber-800` on `amber-50`.
- **Refused / Canceled:** `red-700` on `red-50`.
- **Informational / Queue:** `blue-800` on `blue-100`.