# Примеры UI-компонентов

Контракт, реализация и ключевые стили. Правила, токены и состояния — в [ui-style.md](ui-style.md). Не копируй пример целиком: собери компонент под задачу.

## Button

```tsx
// shared/ui/button/ui.tsx
import type { ButtonHTMLAttributes, ReactNode } from 'react';
import styles from './ui.module.css';

type ButtonView = 'accent' | 'secondary' | 'outline' | 'ghost' | 'text';
type ButtonTone = 'default' | 'danger';
type ButtonSize = 32 | 40 | 48 | 56;

export interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  view?: ButtonView;
  tone?: ButtonTone;
  size?: ButtonSize;
  block?: boolean;
  isLoading?: boolean;
  leftAddon?: ReactNode;
  rightAddon?: ReactNode;
}

export function Button({
  view = 'secondary',
  tone = 'default',
  size = 40,
  block = false,
  isLoading = false,
  leftAddon,
  rightAddon,
  className,
  children,
  disabled,
  type = 'button',
  ...props
}: ButtonProps) {
  const classes = [
    styles.button,
    block && styles.block,
    !children && styles.iconOnly,
    className,
  ]
    .filter(Boolean)
    .join(' ');

  return (
    <button
      {...props}
      type={type}
      className={classes}
      data-view={view}
      data-size={size}
      data-tone={tone}
      data-loading={isLoading || undefined}
      disabled={disabled || isLoading}
      aria-busy={isLoading || undefined}
    >
      <span className={styles.content}>
        {leftAddon && <span className={styles.addon} aria-hidden="true">{leftAddon}</span>}
        {children && <span className={styles.label}>{children}</span>}
        {rightAddon && <span className={styles.addon} aria-hidden="true">{rightAddon}</span>}
      </span>
      {isLoading && <span className={styles.spinner} aria-hidden="true" />}
    </button>
  );
}
```

```css
/* shared/ui/button/ui.module.css */
.button {
  --button-bg: transparent;
  --button-fg: var(--color-text);
  --button-border: transparent;
  --button-hover-bg: transparent;
  --button-hover-border: var(--button-border);
  --button-active-bg: transparent;

  position: relative;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--gap-8);
  border: 1px solid var(--button-border);
  border-radius: var(--radius-control-10);
  background: var(--button-bg);
  color: var(--button-fg);
  font-family: var(--font-sans);
  font-size: var(--text-sm);
  font-weight: 500;
  line-height: 1;
  white-space: nowrap;
  cursor: pointer;
  transition:
    background-color var(--duration-fast) var(--ease-standard),
    border-color var(--duration-fast) var(--ease-standard),
    color var(--duration-fast) var(--ease-standard);
}

.button:focus-visible { outline: 2px solid var(--color-accent); outline-offset: 2px; }
.button:disabled { opacity: 0.45; cursor: not-allowed; }
.button[data-loading]:disabled { opacity: 1; cursor: progress; }

@media (hover: hover) {
  .button:hover:not(:disabled) { background: var(--button-hover-bg); border-color: var(--button-hover-border); }
  .button[data-view='ghost']:hover:not(:disabled) { --button-fg: var(--color-text); }
  .button[data-view='text']:hover:not(:disabled) { --button-fg: var(--color-accent-hover); text-decoration: underline; text-underline-offset: 2px; }
}

.button:active:not(:disabled) { background: var(--button-active-bg); transform: translateY(1px); }

.content { display: inline-flex; align-items: center; gap: inherit; }
.button[data-loading] .content { opacity: 0; }

.spinner {
  position: absolute;
  inset: 0;
  width: 16px;
  height: 16px;
  margin: auto;
  border: 2px solid currentColor;
  border-right-color: transparent;
  border-radius: 50%;
  animation: spin 600ms linear infinite;
}

/* Views: варианты меняют те же компонентные токены */
.button[data-view='accent'] {
  --button-bg: var(--color-accent);
  --button-fg: var(--color-on-accent);
  --button-hover-bg: var(--color-accent-hover);
  --button-active-bg: var(--color-accent-active);
}
.button[data-view='secondary'] {
  --button-bg: var(--color-surface);
  --button-border: var(--color-border-strong);
  --button-hover-bg: var(--color-surface-hover);
  --button-hover-border: var(--color-border-control);
  --button-active-bg: var(--color-surface-active);
}
.button[data-view='outline'] {
  --button-border: var(--color-border-strong);
  --button-hover-bg: var(--color-surface);
  --button-hover-border: var(--color-border-control);
}
.button[data-view='ghost'] { --button-fg: var(--color-text-secondary); --button-hover-bg: var(--color-accent-softer); }
.button[data-view='text'] { --button-fg: var(--color-accent); --button-hover-bg: transparent; }

/* Danger: с secondary/outline — soft-фон, с accent — сплошная заливка */
.button[data-tone='danger'] { --button-fg: var(--color-danger); }
.button[data-tone='danger']:is([data-view='secondary'], [data-view='outline']) {
  --button-bg: var(--color-danger-soft);
  --button-border: transparent;
  --button-hover-bg: var(--color-danger-soft-hover);
  --button-active-bg: var(--color-danger-soft-active);
}
.button[data-tone='danger'][data-view='outline'] { --button-border: var(--color-danger); }
.button[data-tone='danger'][data-view='ghost'] {
  --button-hover-bg: var(--color-danger-soft);
  --button-active-bg: var(--color-danger-soft-hover);
}
.button[data-tone='danger'][data-view='accent'] {
  --button-bg: var(--color-danger);
  --button-fg: var(--color-on-danger);
  --button-hover-bg: var(--color-danger-hover);
  --button-active-bg: var(--color-danger-active);
}

@media (hover: hover) {
  .button[data-tone='danger'][data-view='ghost']:hover:not(:disabled),
  .button[data-tone='danger'][data-view='text']:hover:not(:disabled) { --button-fg: var(--color-danger-hover); }
}

/* Sizes */
.button[data-size='32'] { height: var(--control-32); padding-inline: var(--gap-12); border-radius: var(--radius-control-8); }
.button[data-size='40'] { height: var(--control-40); padding-inline: var(--gap-16); border-radius: var(--radius-control-10); }
.button[data-size='48'] { height: var(--control-48); padding-inline: var(--gap-20); border-radius: var(--radius-control-12); font-size: var(--text-base); }
.button[data-size='56'] { height: var(--control-56); padding-inline: var(--gap-24); border-radius: var(--radius-control-14); font-size: var(--text-md); }

.block { display: flex; width: 100%; }
.iconOnly { aspect-ratio: 1; padding-inline: 0; }

@keyframes spin { to { transform: rotate(360deg); } }
@media (prefers-reduced-motion: reduce) {
  .button { transition: none; }
  .spinner { animation-duration: 1.5s; }
}
```

## TextField

```tsx
// shared/ui/text-field/ui.tsx
import { useId } from 'react';
import type { InputHTMLAttributes, ReactNode } from 'react';
import styles from './ui.module.css';

export interface TextFieldProps
  extends Omit<InputHTMLAttributes<HTMLInputElement>, 'size'> {
  label: string;
  hint?: string;
  error?: string;
  leadingIcon?: ReactNode;
}

export function TextField({
  label,
  hint,
  error,
  leadingIcon,
  id,
  className,
  ...props
}: TextFieldProps) {
  const generatedId = useId();
  const inputId = id ?? generatedId;
  const descriptionId = hint || error ? `${inputId}-description` : undefined;

  return (
    <div className={[styles.field, className].filter(Boolean).join(' ')}>
      <label className={styles.label} htmlFor={inputId}>{label}</label>
      <div className={[styles.control, error ? styles.invalid : ''].filter(Boolean).join(' ')}>
        {leadingIcon && <span className={styles.icon} aria-hidden="true">{leadingIcon}</span>}
        <input
          {...props}
          id={inputId}
          className={styles.input}
          aria-invalid={error ? true : undefined}
          aria-describedby={descriptionId}
        />
      </div>
      {error ? (
        <p className={styles.error} id={descriptionId} role="alert">{error}</p>
      ) : hint ? (
        <p className={styles.hint} id={descriptionId}>{hint}</p>
      ) : null}
    </div>
  );
}
```

Ключевые стили:

```css
/* shared/ui/text-field/ui.module.css */
.field { display: flex; flex-direction: column; gap: var(--gap-8); }
.label { color: var(--color-text-secondary); font-family: var(--font-sans); font-size: var(--text-sm); font-weight: 500; line-height: 1.2; }

.control {
  display: flex;
  align-items: center;
  gap: var(--gap-8);
  height: var(--control-40);
  padding-inline: var(--gap-12);
  border: 1px solid var(--color-border-control);
  border-radius: var(--radius-control-10);
  background: var(--color-surface);
  transition:
    border-color var(--duration-fast) var(--ease-standard),
    box-shadow var(--duration-fast) var(--ease-standard);
}
.control:focus-within { border-color: var(--color-accent); box-shadow: 0 0 0 3px var(--color-accent-soft); }
.invalid { border-color: var(--color-danger); }
.invalid:focus-within { border-color: var(--color-danger); box-shadow: 0 0 0 3px var(--color-danger-soft); }

.input {
  width: 100%;
  min-width: 0;
  border: 0;
  outline: 0;
  background: transparent;
  color: var(--color-text);
  font-family: var(--font-sans);
  font-size: var(--text-base);
  line-height: 1.4;
}
.input::placeholder { color: var(--color-text-muted); }

.hint,
.error { margin: 0; font-family: var(--font-sans); font-size: var(--text-xs); line-height: 1.4; }
.hint { color: var(--color-text-muted); }
.error { color: var(--color-danger); }

@media (max-width: 640px) {
  .input { font-size: var(--text-md); }
}
```
