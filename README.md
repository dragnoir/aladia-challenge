# Aladia challenge


## Explain how developers could use these tokens:

The best way is to:

1- Create a Design System Component Library.

2- Documented tokens

3- Use with Tailwind or SCSS (nject into tailwind.config.js)


Practical notes:

1- Centralize the Tokens

2- Use Semantic Class Names or Utility Classes / Use Token References, Not Raw Values

3- Map Figma Components to Vue Components

4- Implement Code Review Checks( Linter )

5- Sync Tokens from Figma to Code Regularly / Monitoring and Enforcement


# Using Design Tokens in a Vue.js Project

Here's how to integrate the CSS variables and styles from the tokens file into a Vue.js project:

## 1. Installation and Setup

### Option A: Global CSS File (Recommended)
1. Create a `tokens.css` file in `src/assets` folder and use the CSS provided
2. Import it in `main.js` or `main.ts`:

```js
import '@/assets/tokens.css'
```

### Option B: SCSS/SASS Variables (If using SCSS)
1. Create a `_tokens.scss` file in `src/assets` folder
2. Convert the CSS variables to SCSS variables (replace `--` with `$`)
3. Import it in main SCSS file or Vue components

## 2. Using Variables in Vue Components

### In Template (Class-based usage)
```vue
<template>
  <div class="container">
    <h1 class="h1">My Title</h1>
    <p class="paragraph">My content</p>
    <button class="primary-button">Click me</button>
  </div>
</template>
```

### In Component Style (Scoped)
```vue
<style scoped>
.container {
  background-color: var(--surface-page);
  padding: var(--spacing-medium);
}

.primary-button {
  background-color: var(--surface-action);
  color: var(--text-on-action);
  padding: var(--spacing-small) var(--spacing-medium);
  border-radius: var(--border-radius-medium);
}

.primary-button:hover {
  background-color: var(--surface-action-hover);
}
</style>
```

## 3. Theme Switching

Create a theme switcher component:

```vue
<template>
  <button @click="toggleTheme">Toggle Theme</button>
</template>

<script>
export default {
  methods: {
    toggleTheme() {
      const html = document.documentElement
      const currentTheme = html.getAttribute('data-theme')
      const newTheme = currentTheme === 'dark' ? 'light' : 'dark'
      html.setAttribute('data-theme', newTheme)
      localStorage.setItem('theme', newTheme)
    }
  },
  mounted() {
    // Check for saved theme preference
    const savedTheme = localStorage.getItem('theme') || 'light'
    document.documentElement.setAttribute('data-theme', savedTheme)
  }
}
</script>
```

## 4. Responsive Design

Use the spacing variables with media queries or use relative values ( example: em )

```vue
<style scoped>
.card {
  padding: var(--spacing-small);
  
  @media (min-width: 768px) {
    padding: var(--spacing-medium);
  }
}
</style>
```

## 5. Composition API Usage (For JS Access)

Create composable for theme management:

```js
// composables/useTheme.js
import { ref, onMounted } from 'vue'

export default function useTheme() {
  const currentTheme = ref('light')

  const toggleTheme = () => {
    currentTheme.value = currentTheme.value === 'dark' ? 'light' : 'dark'
    document.documentElement.setAttribute('data-theme', currentTheme.value)
    localStorage.setItem('theme', currentTheme.value)
  }

  onMounted(() => {
    const savedTheme = localStorage.getItem('theme') || 'light'
    currentTheme.value = savedTheme
    document.documentElement.setAttribute('data-theme', savedTheme)
  })

  return { currentTheme, toggleTheme }
}
```

Then use it in components:

```vue
<script setup>
import useTheme from '@/composables/useTheme'

const { currentTheme, toggleTheme } = useTheme()
</script>
```

## 6. Example Component Using Tokens

```vue
<template>
  <div class="card" :class="{ 'card--dark': darkMode }">
    <h2 class="h2">{{ title }}</h2>
    <p class="paragraph">{{ content }}</p>
    <button class="card__button" @click="$emit('click')">
      <slot name="button">Default</slot>
    </button>
  </div>
</template>

<script>
export default {
  props: {
    title: String,
    content: String,
    darkMode: Boolean
  }
}
</script>

<style scoped>
.card {
  background: var(--surface-page);
  border: var(--border-width-1) solid var(--border-color-default);
  border-radius: var(--border-radius-medium);
  padding: var(--spacing-medium);
  margin-bottom: var(--spacing-large);
}

.card--dark {
  background: var(--color-neutral-800);
}

.card__button {
  background: var(--surface-action);
  color: var(--text-on-action);
  padding: var(--spacing-small) var(--spacing-medium);
  border: none;
  border-radius: var(--border-radius-small);
  font-family: var(--font-family-primary);
}

.card__button:hover {
  background: var(--surface-action-hover);
}
</style>
```
