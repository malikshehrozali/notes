# Vue.js Complete Beginner's Guide (Vue 3)

A comprehensive tutorial for learning Vue.js from scratch, covering everything you need to build modern web applications.

---

## Table of Contents
1. [What is Vue.js?](#1-what-is-vuejs)
2. [Why Choose Vue.js?](#2-why-choose-vuejs)
3. [Installing Vue](#3-installing-vue)
4. [Understanding the Project Structure](#4-understanding-the-project-structure)
5. [Your First Vue Component](#5-your-first-vue-component)
6. [Template Syntax](#6-template-syntax)
7. [Reactivity: ref vs reactive](#7-reactivity-ref-vs-reactive)
8. [Computed Properties](#8-computed-properties)
9. [Watchers](#9-watchers)
10. [Directives](#10-directives)
11. [Event Handling](#11-event-handling)
12. [Form Input Bindings](#12-form-input-bindings-v-model)
13. [Components Deep Dive](#13-components-deep-dive)
14. [Props - Passing Data to Child Components](#14-props---passing-data-to-child-components)
15. [Emits - Child to Parent Communication](#15-emits---child-to-parent-communication)
16. [Slots - Flexible Component Content](#16-slots---flexible-component-content)
17. [Lifecycle Hooks](#17-lifecycle-hooks)
18. [Composition API vs Options API](#18-composition-api-vs-options-api)
19. [Vue Router - Navigation](#19-vue-router---navigation)
20. [Pinia - State Management](#20-pinia---state-management)
21. [HTTP Requests with Axios](#21-http-requests-with-axios)
22. [Custom Directives](#22-custom-directives)
23. [Teleport](#23-teleport)
24. [Transitions & Animations](#24-transitions--animations)
25. [Best Practices](#25-best-practices)
26. [Building for Production](#26-building-for-production)
27. [Useful Resources](#27-useful-resources)

---

## 1. What is Vue.js?

Vue.js (pronounced "view") is a **progressive JavaScript framework** for building user interfaces. It's designed to be incrementally adoptable, meaning you can use as much or as little of Vue as you need.

**Key Features:**
- **Reactive data binding** - UI automatically updates when data changes
- **Component-based** - Build reusable UI pieces
- **Virtual DOM** - Fast and efficient rendering
- **Small size** - Only ~40KB minified
- **Easy to learn** - Gentle learning curve

**Official Website:** https://vuejs.org

---

## 2. Why Choose Vue.js?

✅ **Simple and intuitive** - Easy to learn if you know HTML, CSS, and JavaScript  
✅ **Great documentation** - Well-written guides and examples  
✅ **Flexible** - Can be used for small widgets or large applications  
✅ **Fast** - Optimized rendering with Virtual DOM  
✅ **Active community** - Large ecosystem of tools and libraries  
✅ **Progressive** - Start small and scale up as needed

---

## 3. Installing Vue

### Method 1: Using CDN (Quick Start)
For simple projects or learning:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Vue App</title>
  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
</head>
<body>
  <div id="app">{{ message }}</div>

  <script>
    const { createApp } = Vue;
    
    createApp({
      data() {
        return {
          message: 'Hello Vue!'
        }
      }
    }).mount('#app');
  </script>
</body>
</html>
```

### Method 2: Using Vite (Recommended for Real Projects)

```bash
# Create a new project
npm create vue@latest

# Follow the prompts, then:
cd your-project-name
npm install
npm run dev
```

**Vite Documentation:** https://vitejs.dev

### Method 3: Using Vue CLI (Alternative)

```bash
npm install -g @vue/cli
vue create my-project
cd my-project
npm run serve
```

---

## 4. Understanding the Project Structure

After creating a Vue project with Vite, you'll see this structure:

```
my-vue-app/
├── node_modules/       # Dependencies
├── public/             # Static files
├── src/
│   ├── assets/         # Images, fonts, etc.
│   ├── components/     # Vue components
│   ├── App.vue         # Root component
│   └── main.js         # Entry point
├── index.html          # HTML template
├── package.json        # Project info & dependencies
└── vite.config.js      # Vite configuration
```

**Key Files Explained:**

- **main.js** - Initializes your Vue app
- **App.vue** - The root component of your application
- **components/** - Where you create reusable components
- **index.html** - The single HTML page (SPA)

---

## 5. Your First Vue Component

A Vue component has three sections:

```vue
<!-- HelloWorld.vue -->
<template>
  <div class="greeting">
    <h1>{{ message }}</h1>
    <button @click="changeMessage">Click Me</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const message = ref('Hello, Vue 3!');

function changeMessage() {
  message.value = 'You clicked the button!';
}
</script>

<style scoped>
.greeting {
  text-align: center;
  color: #42b983;
}
</style>
```

**Three Sections:**
1. **`<template>`** - Your HTML structure
2. **`<script setup>`** - Your JavaScript logic
3. **`<style scoped>`** - Your CSS (scoped to this component)

---

## 6. Template Syntax

### Text Interpolation
```vue
<p>{{ message }}</p>
<p>{{ count + 1 }}</p>
<p>{{ ok ? 'YES' : 'NO' }}</p>
```

### Raw HTML
```vue
<div v-html="htmlContent"></div>
```

### Attribute Binding
```vue
<img :src="imageUrl" :alt="imageAlt">
<div :class="className"></div>
<button :disabled="isDisabled">Submit</button>
```

### JavaScript Expressions
```vue
<p>{{ message.split('').reverse().join('') }}</p>
<p>{{ user.firstName + ' ' + user.lastName }}</p>
```

---

## 7. Reactivity: ref vs reactive

Vue 3 uses the **Composition API** for reactivity.

### Using `ref` (For Primitives)

```vue
<script setup>
import { ref } from 'vue';

// Create reactive variables
const count = ref(0);
const name = ref('John');
const isActive = ref(true);

// Access/modify with .value
function increment() {
  count.value++;
}

function updateName() {
  name.value = 'Jane';
}
</script>

<template>
  <p>Count: {{ count }}</p>
  <p>Name: {{ name }}</p>
  <button @click="increment">Increment</button>
</template>
```

**Important:** In `<script>`, use `.value`. In `<template>`, omit `.value`.

### Using `reactive` (For Objects)

```vue
<script setup>
import { reactive } from 'vue';

const user = reactive({
  name: 'Ali',
  age: 22,
  email: 'ali@example.com'
});

function updateUser() {
  user.name = 'Ahmed';
  user.age = 23;
}
</script>

<template>
  <div>
    <p>Name: {{ user.name }}</p>
    <p>Age: {{ user.age }}</p>
    <button @click="updateUser">Update</button>
  </div>
</template>
```

**When to use what:**
- Use `ref` for primitive values (strings, numbers, booleans)
- Use `reactive` for objects and arrays
- Both are reactive and update the UI automatically

---

## 8. Computed Properties

Computed properties are **cached** and only re-calculate when dependencies change.

```vue
<script setup>
import { ref, computed } from 'vue';

const firstName = ref('John');
const lastName = ref('Doe');

// Computed property
const fullName = computed(() => {
  return firstName.value + ' ' + lastName.value;
});

const price = ref(100);
const quantity = ref(3);

// Computed with calculation
const totalPrice = computed(() => {
  return price.value * quantity.value;
});
</script>

<template>
  <p>Full Name: {{ fullName }}</p>
  <p>Total: ${{ totalPrice }}</p>
</template>
```

**Benefits:**
- Cached based on dependencies
- Only re-runs when dependencies change
- Better performance than methods

---

## 9. Watchers

Watchers let you perform side effects when reactive data changes.

### Basic Watch

```vue
<script setup>
import { ref, watch } from 'vue';

const question = ref('');
const answer = ref('');

// Watch a single ref
watch(question, (newValue, oldValue) => {
  console.log(`Changed from "${oldValue}" to "${newValue}"`);
  answer.value = 'Thinking...';
});
</script>
```

### Watch Multiple Sources

```vue
<script setup>
import { ref, watch } from 'vue';

const firstName = ref('');
const lastName = ref('');

watch([firstName, lastName], ([newFirst, newLast]) => {
  console.log(`Name: ${newFirst} ${newLast}`);
});
</script>
```

### watchEffect (Automatic Dependencies)

```vue
<script setup>
import { ref, watchEffect } from 'vue';

const count = ref(0);

// Automatically tracks dependencies
watchEffect(() => {
  console.log(`Count is: ${count.value}`);
});
</script>
```

**Difference:**
- `watch` - You specify what to watch
- `watchEffect` - Automatically tracks all reactive dependencies

---

## 10. Directives

Directives are special attributes that start with `v-`.

### v-if, v-else-if, v-else (Conditional Rendering)

```vue
<template>
  <div>
    <p v-if="score >= 90">Excellent!</p>
    <p v-else-if="score >= 70">Good Job!</p>
    <p v-else-if="score >= 50">Pass</p>
    <p v-else>Fail</p>
  </div>
</template>

<script setup>
import { ref } from 'vue';
const score = ref(85);
</script>
```

### v-show (Toggle Visibility)

```vue
<template>
  <p v-show="isVisible">This can be toggled</p>
  <button @click="isVisible = !isVisible">Toggle</button>
</template>

<script setup>
import { ref } from 'vue';
const isVisible = ref(true);
</script>
```

**v-if vs v-show:**
- `v-if` - Removes/adds element from DOM (better for rarely toggled)
- `v-show` - Just toggles CSS display (better for frequently toggled)

### v-for (List Rendering)

```vue
<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
  
  <!-- With index -->
  <ul>
    <li v-for="(item, index) in items" :key="item.id">
      {{ index + 1 }}. {{ item.name }}
    </li>
  </ul>
  
  <!-- Object iteration -->
  <ul>
    <li v-for="(value, key) in user" :key="key">
      {{ key }}: {{ value }}
    </li>
  </ul>
</template>

<script setup>
import { ref } from 'vue';

const items = ref([
  { id: 1, name: 'Apple' },
  { id: 2, name: 'Banana' },
  { id: 3, name: 'Orange' }
]);

const user = ref({
  name: 'John',
  age: 30,
  email: 'john@example.com'
});
</script>
```

**Important:** Always use `:key` with unique values for performance.

### v-bind (Attribute Binding)

```vue
<template>
  <!-- Full syntax -->
  <img v-bind:src="imageUrl" v-bind:alt="imageAlt">
  
  <!-- Shorthand (preferred) -->
  <img :src="imageUrl" :alt="imageAlt">
  
  <!-- Dynamic class -->
  <div :class="{ active: isActive, 'text-danger': hasError }"></div>
  
  <!-- Dynamic style -->
  <div :style="{ color: textColor, fontSize: fontSize + 'px' }"></div>
  
  <!-- Multiple attributes -->
  <button :disabled="isDisabled" :class="buttonClass">
    Click Me
  </button>
</template>

<script setup>
import { ref } from 'vue';

const imageUrl = ref('/path/to/image.jpg');
const imageAlt = ref('Description');
const isActive = ref(true);
const hasError = ref(false);
const textColor = ref('blue');
const fontSize = ref(16);
</script>
```

### v-on (Event Handling)

```vue
<template>
  <!-- Full syntax -->
  <button v-on:click="handleClick">Click</button>
  
  <!-- Shorthand (preferred) -->
  <button @click="handleClick">Click</button>
  
  <!-- Inline handler -->
  <button @click="count++">Increment</button>
  
  <!-- With event object -->
  <button @click="handleClick($event)">Click</button>
  
  <!-- Multiple events -->
  <input 
    @input="handleInput"
    @focus="handleFocus"
    @blur="handleBlur"
  >
  
  <!-- Event modifiers -->
  <form @submit.prevent="onSubmit">
    <button type="submit">Submit</button>
  </form>
  
  <a @click.stop="doThis">Link</a>
</template>

<script setup>
import { ref } from 'vue';

const count = ref(0);

function handleClick(event) {
  console.log('Button clicked!', event);
}

function onSubmit() {
  console.log('Form submitted');
}
</script>
```

**Common Event Modifiers:**
- `.prevent` - Prevents default behavior
- `.stop` - Stops event propagation
- `.once` - Trigger only once
- `.capture` - Use capture mode

### v-text & v-html

```vue
<template>
  <!-- v-text (safe, escapes HTML) -->
  <p v-text="message"></p>
  <!-- Same as: <p>{{ message }}</p> -->
  
  <!-- v-html (renders HTML, use carefully!) -->
  <div v-html="htmlContent"></div>
</template>

<script setup>
import { ref } from 'vue';

const message = ref('Hello World');
const htmlContent = ref('<strong>Bold Text</strong>');
</script>
```

**Warning:** Only use `v-html` with trusted content to avoid XSS attacks.

---

## 11. Event Handling

### Basic Events

```vue
<template>
  <button @click="handleClick">Click Me</button>
  <input @input="handleInput" v-model="text">
  <div @mouseenter="handleMouseEnter">Hover me</div>
</template>

<script setup>
import { ref } from 'vue';

const text = ref('');

function handleClick() {
  alert('Button clicked!');
}

function handleInput(event) {
  console.log('Input value:', event.target.value);
}

function handleMouseEnter() {
  console.log('Mouse entered!');
}
</script>
```

### Passing Arguments

```vue
<template>
  <button @click="greet('Hello')">Say Hello</button>
  <button @click="greet('Goodbye')">Say Goodbye</button>
  
  <!-- Pass both argument and event -->
  <button @click="handleClick('test', $event)">
    Click with Args
  </button>
</template>

<script setup>
function greet(message) {
  alert(message);
}

function handleClick(arg, event) {
  console.log('Argument:', arg);
  console.log('Event:', event);
}
</script>
```

### Key Modifiers

```vue
<template>
  <!-- Enter key -->
  <input @keyup.enter="submitForm">
  
  <!-- Escape key -->
  <input @keyup.esc="clearInput">
  
  <!-- Ctrl + Enter -->
  <input @keyup.ctrl.enter="sendMessage">
  
  <!-- Multiple keys -->
  <input @keyup.delete="handleDelete">
</template>
```

**Common Key Modifiers:**
- `.enter`, `.tab`, `.delete`, `.esc`, `.space`
- `.up`, `.down`, `.left`, `.right`
- `.ctrl`, `.alt`, `.shift`, `.meta`

---

## 12. Form Input Bindings (v-model)

`v-model` creates **two-way data binding** on form inputs.

### Text Input

```vue
<template>
  <input v-model="message" placeholder="Enter text">
  <p>Message: {{ message }}</p>
</template>

<script setup>
import { ref } from 'vue';
const message = ref('');
</script>
```

### Textarea

```vue
<template>
  <textarea v-model="description" rows="5"></textarea>
  <p>{{ description }}</p>
</template>

<script setup>
import { ref } from 'vue';
const description = ref('');
</script>
```

### Checkbox

```vue
<template>
  <!-- Single checkbox -->
  <input type="checkbox" v-model="agreed">
  <label>I agree to terms</label>
  <p>Agreed: {{ agreed }}</p>
  
  <!-- Multiple checkboxes -->
  <input type="checkbox" value="Vue" v-model="frameworks">
  <input type="checkbox" value="React" v-model="frameworks">
  <input type="checkbox" value="Angular" v-model="frameworks">
  <p>Selected: {{ frameworks }}</p>
</template>

<script setup>
import { ref } from 'vue';

const agreed = ref(false);
const frameworks = ref([]);
</script>
```

### Radio Buttons

```vue
<template>
  <input type="radio" value="male" v-model="gender">
  <label>Male</label>
  
  <input type="radio" value="female" v-model="gender">
  <label>Female</label>
  
  <p>Selected: {{ gender }}</p>
</template>

<script setup>
import { ref } from 'vue';
const gender = ref('');
</script>
```

### Select Dropdown

```vue
<template>
  <!-- Single select -->
  <select v-model="selected">
    <option disabled value="">Please select</option>
    <option value="a">Option A</option>
    <option value="b">Option B</option>
    <option value="c">Option C</option>
  </select>
  <p>Selected: {{ selected }}</p>
  
  <!-- Multiple select -->
  <select v-model="multiSelected" multiple>
    <option value="vue">Vue</option>
    <option value="react">React</option>
    <option value="angular">Angular</option>
  </select>
  <p>Selected: {{ multiSelected }}</p>
</template>

<script setup>
import { ref } from 'vue';

const selected = ref('');
const multiSelected = ref([]);
</script>
```

### v-model Modifiers

```vue
<template>
  <!-- .lazy - Update on change event (not input) -->
  <input v-model.lazy="msg">
  
  <!-- .number - Convert to number automatically -->
  <input v-model.number="age" type="number">
  
  <!-- .trim - Remove whitespace automatically -->
  <input v-model.trim="username">
</template>

<script setup>
import { ref } from 'vue';

const msg = ref('');
const age = ref(0);
const username = ref('');
</script>
```

---

## 13. Components Deep Dive

Components are reusable Vue instances with a name.

### Creating a Component

```vue
<!-- components/UserCard.vue -->
<template>
  <div class="user-card">
    <img :src="avatar" alt="Avatar">
    <h3>{{ name }}</h3>
    <p>{{ email }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const avatar = ref('/path/to/avatar.jpg');
const name = ref('John Doe');
const email = ref('john@example.com');
</script>

<style scoped>
.user-card {
  border: 1px solid #ddd;
  padding: 20px;
  border-radius: 8px;
}
</style>
```

### Using the Component

```vue
<!-- App.vue -->
<template>
  <div>
    <UserCard />
    <UserCard />
    <UserCard />
  </div>
</template>

<script setup>
import UserCard from './components/UserCard.vue';
</script>
```

### Component Registration

**Local Registration (Recommended):**
```vue
<script setup>
import MyComponent from './components/MyComponent.vue';
// Automatically available in template
</script>
```

**Global Registration:**
```js
// main.js
import { createApp } from 'vue';
import App from './App.vue';
import MyComponent from './components/MyComponent.vue';

const app = createApp(App);
app.component('MyComponent', MyComponent);
app.mount('#app');
```

---

## 14. Props - Passing Data to Child Components

Props allow you to pass data from parent to child components.

### Basic Props

```vue
<!-- Child.vue -->
<template>
  <div>
    <h2>{{ title }}</h2>
    <p>{{ description }}</p>
  </div>
</template>

<script setup>
// Simple props
defineProps(['title', 'description']);
</script>
```

### Props with Types

```vue
<!-- Child.vue -->
<template>
  <div class="user-profile">
    <h2>{{ user.name }}</h2>
    <p>Age: {{ user.age }}</p>
    <p>Active: {{ isActive ? 'Yes' : 'No' }}</p>
  </div>
</template>

<script setup>
// Props with validation
defineProps({
  user: {
    type: Object,
    required: true
  },
  isActive: {
    type: Boolean,
    default: false
  },
  count: {
    type: Number,
    default: 0
  }
});
</script>
```

### Using Props in Parent

```vue
<!-- Parent.vue -->
<template>
  <Child 
    title="Hello World"
    description="This is a description"
  />
  
  <UserProfile 
    :user="userData"
    :is-active="true"
    :count="5"
  />
</template>

<script setup>
import Child from './components/Child.vue';
import UserProfile from './components/UserProfile.vue';
import { reactive } from 'vue';

const userData = reactive({
  name: 'John Doe',
  age: 30
});
</script>
```

### Accessing Props

```vue
<script setup>
const props = defineProps({
  title: String,
  count: Number
});

// Access props
console.log(props.title);
console.log(props.count);

// Use in computed
import { computed } from 'vue';
const doubleCount = computed(() => props.count * 2);
</script>
```

**Important Rules:**
- Props are **read-only** in child components
- Always use `:prop-name` for dynamic values
- Use `prop-name` for static strings

---

## 15. Emits - Child to Parent Communication

Emits allow child components to send data back to parent components.

### Basic Emit

```vue
<!-- Child.vue -->
<template>
  <button @click="sendMessage">Send to Parent</button>
</template>

<script setup>
const emit = defineEmits(['message']);

function sendMessage() {
  emit('message', 'Hello from child!');
}
</script>
```

### Parent Receiving the Emit

```vue
<!-- Parent.vue -->
<template>
  <Child @message="handleMessage" />
  <p>Received: {{ receivedMessage }}</p>
</template>

<script setup>
import Child from './components/Child.vue';
import { ref } from 'vue';

const receivedMessage = ref('');

function handleMessage(msg) {
  receivedMessage.value = msg;
}
</script>
```

### Multiple Emits with Validation

```vue
<!-- Counter.vue -->
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
    <button @click="reset">Reset</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const emit = defineEmits({
  increment: null,
  decrement: null,
  reset: (value) => {
    // Validation: must be 0
    return value === 0;
  }
});

const count = ref(0);

function increment() {
  count.value++;
  emit('increment', count.value);
}

function decrement() {
  count.value--;
  emit('decrement', count.value);
}

function reset() {
  count.value = 0;
  emit('reset', 0);
}
</script>
```

### Parent Handling Multiple Emits

```vue
<!-- Parent.vue -->
<template>
  <Counter 
    @increment="onIncrement"
    @decrement="onDecrement"
    @reset="onReset"
  />
  <p>Total operations: {{ operations }}</p>
</template>

<script setup>
import Counter from './components/Counter.vue';
import { ref } from 'vue';

const operations = ref(0);

function onIncrement(value) {
  console.log('Incremented to:', value);
  operations.value++;
}

function onDecrement(value) {
  console.log('Decremented to:', value);
  operations.value++;
}

function onReset() {
  console.log('Reset!');
  operations.value = 0;
}
</script>
```

---

## 16. Slots - Flexible Component Content

Slots allow you to pass template content from parent to child.

### Basic Slot

```vue
<!-- Card.vue -->
<template>
  <div class="card">
    <div class="card-body">
      <slot></slot>
    </div>
  </div>
</template>

<style scoped>
.card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 20px;
}
</style>
```

**Using the Card:**
```vue
<template>
  <Card>
    <h2>Welcome</h2>
    <p>This content goes into the slot</p>
  </Card>
</template>
```

### Named Slots

```vue
<!-- Layout.vue -->
<template>
  <div class="layout">
    <header>
      <slot name="header"></slot>
    </header>
    
    <main>
      <slot></slot> <!-- Default slot -->
    </main>
    
    <footer>
      <slot name="footer"></slot>
    </footer>
  </div>
</template>
```

**Using Named Slots:**
```vue
<template>
  <Layout>
    <template #header>
      <h1>Page Title</h1>
    </template>
    
    <p>Main content goes here</p>
    
    <template #footer>
      <p>&copy; 2024 My Site</p>
    </template>
  </Layout>
</template>
```

### Scoped Slots (Advanced)

```vue
<!-- List.vue -->
<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      <slot :item="item" :index="item.id"></slot>
    </li>
  </ul>
</template>

<script setup>
defineProps({
  items: Array
});
</script>
```

**Using Scoped Slots:**
```vue
<template>
  <List :items="users">
    <template #default="{ item, index }">
      <strong>{{ index }}:</strong> {{ item.name }}
    </template>
  </List