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
  </List>
</template>

<script setup>
import { ref } from 'vue';

const users = ref([
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
]);
</script>
```

### Default Slot Content

```vue
<!-- Button.vue -->
<template>
  <button class="custom-btn">
    <slot>Default Button Text</slot>
  </button>
</template>
```

If no content is provided, "Default Button Text" will appear.

---

## 17. Lifecycle Hooks

Lifecycle hooks let you run code at specific stages of a component's life.

### Common Lifecycle Hooks

```vue
<script setup>
import { 
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted
} from 'vue';

// Before component is mounted to DOM
onBeforeMount(() => {
  console.log('Before Mount');
});

// After component is mounted (most common)
onMounted(() => {
  console.log('Component Mounted!');
  // Good for: API calls, DOM manipulation, timers
});

// Before component updates
onBeforeUpdate(() => {
  console.log('Before Update');
});

// After component updates
onUpdated(() => {
  console.log('Component Updated!');
});

// Before component is destroyed
onBeforeUnmount(() => {
  console.log('Before Unmount');
  // Good for: cleanup
});

// After component is destroyed
onUnmounted(() => {
  console.log('Component Unmounted!');
  // Good for: cleanup, removing event listeners
});
</script>
```

### Practical Example - Fetching Data

```vue
<template>
  <div>
    <h2>Users</h2>
    <p v-if="loading">Loading...</p>
    <ul v-else>
      <li v-for="user in users" :key="user.id">
        {{ user.name }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const users = ref([]);
const loading = ref(true);
let timer = null;

onMounted(async () => {
  console.log('Component mounted, fetching data...');
  
  // Simulate API call
  setTimeout(() => {
    users.value = [
      { id: 1, name: 'John' },
      { id: 2, name: 'Jane' }
    ];
    loading.value = false;
  }, 1000);
  
  // Set up a timer
  timer = setInterval(() => {
    console.log('Timer running...');
  }, 1000);
});

onUnmounted(() => {
  console.log('Cleaning up...');
  // Clear timer when component is destroyed
  if (timer) {
    clearInterval(timer);
  }
});
</script>
```

### Lifecycle Hook Diagram

```
Creation Phase:
  setup() → onBeforeMount() → onMounted()

Update Phase:
  onBeforeUpdate() → onUpdated()

Destruction Phase:
  onBeforeUnmount() → onUnmounted()
```

---

## 18. Composition API vs Options API

Vue 3 supports both APIs. Composition API is newer and recommended.

### Options API (Vue 2 Style)

```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double: {{ doubleCount }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      count: 0
    };
  },
  
  computed: {
    doubleCount() {
      return this.count * 2;
    }
  },
  
  methods: {
    increment() {
      this.count++;
    }
  },
  
  mounted() {
    console.log('Component mounted');
  }
};
</script>
```

### Composition API (Vue 3 Style - Recommended)

```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double: {{ doubleCount }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const count = ref(0);

const doubleCount = computed(() => count.value * 2);

function increment() {
  count.value++;
}

onMounted(() => {
  console.log('Component mounted');
});
</script>
```

### Why Composition API?

✅ **Better code organization** - Group related logic together  
✅ **Reusable logic** - Easy to extract and share via composables  
✅ **Better TypeScript support**  
✅ **No `this` keyword** - Less confusion  
✅ **Tree-shaking friendly** - Smaller bundle size

### Creating Reusable Logic (Composables)

```js
// composables/useCounter.js
import { ref, computed } from 'vue';

export function useCounter(initialValue = 0) {
  const count = ref(initialValue);
  
  const doubleCount = computed(() => count.value * 2);
  
  function increment() {
    count.value++;
  }
  
  function decrement() {
    count.value--;
  }
  
  return {
    count,
    doubleCount,
    increment,
    decrement
  };
}
```

**Using the Composable:**

```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double: {{ doubleCount }}</p>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
  </div>
</template>

<script setup>
import { useCounter } from './composables/useCounter.js';

const { count, doubleCount, increment, decrement } = useCounter(10);
</script>
```

---

## 19. Vue Router - Navigation

Vue Router enables navigation between different pages/views in your app.

### Installation

```bash
npm install vue-router@4
```

### Basic Setup

**1. Create router configuration:**

```js
// router/index.js
import { createRouter, createWebHistory } from 'vue-router';
import Home from '../views/Home.vue';
import About from '../views/About.vue';
import Contact from '../views/Contact.vue';

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: About
  },
  {
    path: '/contact',
    name: 'Contact',
    component: Contact
  },
  {
    path: '/user/:id',
    name: 'User',
    component: () => import('../views/User.vue') // Lazy loading
  }
];

const router = createRouter({
  history: createWebHistory(),
  routes
});

export default router;
```

**2. Register router in main.js:**

```js
// main.js
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';

const app = createApp(App);
app.use(router);
app.mount('#app');
```

**3. Use router-view in App.vue:**

```vue
<!-- App.vue -->
<template>
  <div id="app">
    <nav>
      <router-link to="/">Home</router-link>
      <router-link to="/about">About</router-link>
      <router-link to="/contact">Contact</router-link>
    </nav>
    
    <router-view></router-view>
  </div>
</template>

<style>
nav {
  padding: 20px;
  background: #f0f0f0;
}

nav a {
  margin-right: 10px;
  text-decoration: none;
  color: #42b983;
}

nav a.router-link-active {
  font-weight: bold;
}
</style>
```

### Navigation Methods

```vue
<template>
  <div>
    <!-- Declarative navigation -->
    <router-link to="/about">About</router-link>
    <router-link :to="{ name: 'User', params: { id: 123 } }">
      User 123
    </router-link>
    
    <!-- Programmatic navigation -->
    <button @click="goToAbout">Go to About</button>
    <button @click="goBack">Go Back</button>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router';

const router = useRouter();

function goToAbout() {
  router.push('/about');
  // Or: router.push({ name: 'About' });
}

function goBack() {
  router.back();
}
</script>
```

### Route Parameters

```vue
<!-- User.vue -->
<template>
  <div>
    <h2>User Profile</h2>
    <p>User ID: {{ userId }}</p>
  </div>
</template>

<script setup>
import { useRoute } from 'vue-router';
import { computed } from 'vue';

const route = useRoute();

// Access route params
const userId = computed(() => route.params.id);

// Access query parameters
// Example: /user/123?tab=profile
const tab = computed(() => route.query.tab);
</script>
```

### Navigation Guards

```js
// router/index.js
const router = createRouter({
  // ... routes
});

// Global before guard
router.beforeEach((to, from, next) => {
  const isAuthenticated = false; // Check auth status
  
  if (to.name !== 'Login' && !isAuthenticated) {
    next({ name: 'Login' });
  } else {
    next();
  }
});

export default router;
```

### Nested Routes

```js
const routes = [
  {
    path: '/dashboard',
    component: Dashboard,
    children: [
      {
        path: 'profile',
        component: Profile
      },
      {
        path: 'settings',
        component: Settings
      }
    ]
  }
];
```

**Vue Router Documentation:** https://router.vuejs.org

---

## 20. Pinia - State Management

Pinia is the official state management library for Vue 3.

### Installation

```bash
npm install pinia
```

### Setup

```js
// main.js
import { createApp } from 'vue';
import { createPinia } from 'pinia';
import App from './App.vue';

const app = createApp(App);
const pinia = createPinia();

app.use(pinia);
app.mount('#app');
```

### Creating a Store

```js
// stores/counter.js
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useCounterStore = defineStore('counter', () => {
  // State
  const count = ref(0);
  
  // Getters (computed)
  const doubleCount = computed(() => count.value * 2);
  
  // Actions (methods)
  function increment() {
    count.value++;
  }
  
  function decrement() {
    count.value--;
  }
  
  function incrementBy(amount) {
    count.value += amount;
  }
  
  return {
    count,
    doubleCount,
    increment,
    decrement,
    incrementBy
  };
});
```

### Using Store in Components

```vue
<template>
  <div>
    <h2>Counter Store Demo</h2>
    <p>Count: {{ counter.count }}</p>
    <p>Double: {{ counter.doubleCount }}</p>
    
    <button @click="counter.increment">+</button>
    <button @click="counter.decrement">-</button>
    <button @click="counter.incrementBy(5)">+5</button>
  </div>
</template>

<script setup>
import { useCounterStore } from '@/stores/counter';

const counter = useCounterStore();
</script>
```

### User Store Example

```js
// stores/user.js
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useUserStore = defineStore('user', () => {
  // State
  const user = ref(null);
  const isLoading = ref(false);
  
  // Getters
  const isLoggedIn = computed(() => user.value !== null);
  const userName = computed(() => user.value?.name || 'Guest');
  
  // Actions
  async function login(email, password) {
    isLoading.value = true;
    
    try {
      // Simulate API call
      const response = await fetch('/api/login', {
        method: 'POST',
        body: JSON.stringify({ email, password })
      });
      
      const data = await response.json();
      user.value = data.user;
      
      return true;
    } catch (error) {
      console.error('Login failed:', error);
      return false;
    } finally {
      isLoading.value = false;
    }
  }
  
  function logout() {
    user.value = null;
  }
  
  return {
    user,
    isLoading,
    isLoggedIn,
    userName,
    login,
    logout
  };
});
```

### Using Multiple Stores

```vue
<template>
  <div>
    <p>Count: {{ counter.count }}</p>
    <p>User: {{ user.userName }}</p>
    <p>Logged In: {{ user.isLoggedIn }}</p>
  </div>
</template>

<script setup>
import { useCounterStore } from '@/stores/counter';
import { useUserStore } from '@/stores/user';

const counter = useCounterStore();
const user = useUserStore();
</script>
```

### Store with Options API Style

```js
// stores/todo.js
import { defineStore } from 'pinia';

export const useTodoStore = defineStore('todo', {
  state: () => ({
    todos: [],
    filter: 'all'
  }),
  
  getters: {
    completedTodos: (state) => {
      return state.todos.filter(todo => todo.completed);
    },
    
    activeTodos: (state) => {
      return state.todos.filter(todo => !todo.completed);
    }
  },
  
  actions: {
    addTodo(text) {
      this.todos.push({
        id: Date.now(),
        text,
        completed: false
      });
    },
    
    removeTodo(id) {
      this.todos = this.todos.filter(todo => todo.id !== id);
    },
    
    toggleTodo(id) {
      const todo = this.todos.find(todo => todo.id === id);
      if (todo) {
        todo.completed = !todo.completed;
      }
    }
  }
});
```

**Pinia Documentation:** https://pinia.vuejs.org

---

## 21. HTTP Requests with Axios

Axios is a popular HTTP client for making API requests.

### Installation

```bash
npm install axios
```

### Basic GET Request

```vue
<template>
  <div>
    <h2>Users</h2>
    
    <p v-if="loading">Loading...</p>
    <p v-else-if="error">Error: {{ error }}</p>
    
    <ul v-else>
      <li v-for="user in users" :key="user.id">
        {{ user.name }} - {{ user.email }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const users = ref([]);
const loading = ref(false);
const error = ref(null);

async function fetchUsers() {
  loading.value = true;
  error.value = null;
  
  try {
    const response = await axios.get('https://jsonplaceholder.typicode.com/users');
    users.value = response.data;
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
}

onMounted(() => {
  fetchUsers();
});
</script>
```

### POST Request

```vue
<template>
  <div>
    <h2>Create Post</h2>
    
    <input v-model="title" placeholder="Title">
    <textarea v-model="body" placeholder="Body"></textarea>
    <button @click="createPost" :disabled="loading">
      {{ loading ? 'Creating...' : 'Create Post' }}
    </button>
    
    <div v-if="successMessage">{{ successMessage }}</div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

const title = ref('');
const body = ref('');
const loading = ref(false);
const successMessage = ref('');

async function createPost() {
  loading.value = true;
  successMessage.value = '';
  
  try {
    const response = await axios.post('https://jsonplaceholder.typicode.com/posts', {
      title: title.value,
      body: body.value,
      userId: 1
    });
    
    console.log('Created:', response.data);
    successMessage.value = 'Post created successfully!';
    
    // Clear form
    title.value = '';
    body.value = '';
  } catch (err) {
    console.error('Error:', err);
  } finally {
    loading.value = false;
  }
}
</script>
```

### PUT/PATCH Request (Update)

```vue
<script setup>
import axios from 'axios';

async function updateUser(userId, data) {
  try {
    const response = await axios.put(`https://api.example.com/users/${userId}`, data);
    console.log('Updated:', response.data);
  } catch (err) {
    console.error('Error updating:', err);
  }
}

// Or use PATCH for partial updates
async function patchUser(userId, data) {
  try {
    const response = await axios.patch(`https://api.example.com/users/${userId}`, data);
    console.log('Patched:', response.data);
  } catch (err) {
    console.error('Error patching:', err);
  }
}
</script>
```

### DELETE Request

```vue
<script setup>
import axios from 'axios';

async function deleteUser(userId) {
  try {
    await axios.delete(`https://api.example.com/users/${userId}`);
    console.log('User deleted');
  } catch (err) {
    console.error('Error deleting:', err);
  }
}
</script>
```

### Creating Axios Instance (Recommended)

```js
// api/axios.js
import axios from 'axios';

const api = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
});

// Add request interceptor (for auth tokens, etc.)
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Add response interceptor (for error handling)
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Handle unauthorized
      console.log('Unauthorized - redirect to login');
    }
    return Promise.reject(error);
  }
);

export default api;
```

**Using the custom axios instance:**

```vue
<script setup>
import api from '@/api/axios';

async function fetchData() {
  try {
    const response = await api.get('/users');
    console.log(response.data);
  } catch (err) {
    console.error(err);
  }
}
</script>
```

### Reusable Composable for API Calls

```js
// composables/useFetch.js
import { ref } from 'vue';
import axios from 'axios';

export function useFetch(url) {
  const data = ref(null);
  const error = ref(null);
  const loading = ref(false);
  
  async function fetchData() {
    loading.value = true;
    error.value = null;
    
    try {
      const response = await axios.get(url);
      data.value = response.data;
    } catch (err) {
      error.value = err.message;
    } finally {
      loading.value = false;
    }
  }
  
  return {
    data,
    error,
    loading,
    fetchData
  };
}
```

**Using the composable:**

```vue
<script setup>
import { onMounted } from 'vue';
import { useFetch } from '@/composables/useFetch';

const { data: users, error, loading, fetchData } = useFetch(
  'https://jsonplaceholder.typicode.com/users'
);

onMounted(() => {
  fetchData();
});
</script>
```

**Axios Documentation:** https://axios-http.com

---

## 22. Custom Directives

Create your own directives for DOM manipulation.

### Basic Custom Directive

```js
// main.js
import { createApp } from 'vue';
import App from './App.vue';

const app = createApp(App);

// Global directive
app.directive('focus', {
  mounted(el) {
    el.focus();
  }
});

app.mount('#app');
```

**Using the directive:**

```vue
<template>
  <input v-focus placeholder="Auto-focused">
</template>
```

### Directive with Arguments

```js
// main.js
app.directive('color', {
  mounted(el, binding) {
    el.style.color = binding.value;
  },
  updated(el, binding) {
    el.style.color = binding.value;
  }
});
```

**Usage:**

```vue
<template>
  <p v-color="'red'">Red text</p>
  <p v-color="textColor">Dynamic color</p>
</template>

<script setup>
import { ref } from 'vue';
const textColor = ref('blue');
</script>
```

### Local Directive (Component-specific)

```vue
<template>
  <input v-focus>
  <p v-highlight="'yellow'">Highlighted text</p>
</template>

<script setup>
// Local directives
const vFocus = {
  mounted(el) {
    el.focus();
  }
};

const vHighlight = {
  mounted(el, binding) {
    el.style.backgroundColor = binding.value;
  }
};
</script>
```

### Directive Hooks

```js
app.directive('example', {
  // Called before element is inserted into DOM
  created(el, binding, vnode) {
    console.log('created');
  },
  
  // Called before parent component is mounted
  beforeMount(el, binding, vnode) {
    console.log('beforeMount');
  },
  
  // Called when parent component is mounted
  mounted(el, binding, vnode) {
    console.log('mounted');
  },
  
  // Called before parent component is updated
  beforeUpdate(el, binding, vnode, prevVnode) {
    console.log('beforeUpdate');
  },
  
  // Called after parent component is updated
  updated(el, binding, vnode, prevVnode) {
    console.log('updated');
  },
  
  // Called before parent component is unmounted
  beforeUnmount(el, binding, vnode) {
    console.log('beforeUnmount');
  },
  
  // Called when parent component is unmounted
  unmounted(el, binding, vnode) {
    console.log('unmounted');
  }
});
```

### Practical Examples

**Click Outside Directive:**

```js
app.directive('click-outside', {
  mounted(el, binding) {
    el.clickOutsideEvent = function(event) {
      if (!(el === event.target || el.contains(event.target))) {
        binding.value(event);
      }
    };
    document.addEventListener('click', el.clickOutsideEvent);
  },
  unmounted(el) {
    document.removeEventListener('click', el.clickOutsideEvent);
  }
});
```

**Usage:**

```vue
<template>
  <div v-click-outside="closeMenu" class="dropdown">
    <button @click="isOpen = !isOpen">Menu</button>
    <ul v-if="isOpen">
      <li>Item 1</li>
      <li>Item 2</li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const isOpen = ref(false);

function closeMenu() {
  isOpen.value = false;
}
</script>
```

**Lazy Load Image Directive:**

```js
app.directive('lazy', {
  mounted(el, binding) {
    const observer = new IntersectionObserver(entries => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          el.src = binding.value;
          observer.disconnect();
        }
      });
    });
    
    observer.observe(el);
  }
});
```

**Usage:**

```vue
<template>
  <img v-lazy="imageUrl" alt="Lazy loaded">
</template>
```

---

## 23. Teleport

Teleport allows you to render content in a different part of the DOM.

### Basic Teleport

```vue
<!-- Component.vue -->
<template>
  <div>
    <h2>My Component</h2>
    
    <teleport to="body">
      <div class="modal">
        <p>This modal is rendered at the end of body</p>
      </div>
    </teleport>
  </div>
</template>

<style>
.modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: white;
  padding: 20px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.3);
}
</style>
```

### Modal Component with Teleport

```vue
<!-- Modal.vue -->
<template>
  <teleport to="body">
    <div v-if="isOpen" class="modal-overlay" @click="close">
      <div class="modal-content" @click.stop>
        <button class="close-btn" @click="close">×</button>
        <slot></slot>
      </div>
    </div>
  </teleport>
</template>

<script setup>
defineProps({
  isOpen: Boolean
});

const emit = defineEmits(['close']);

function close() {
  emit('close');
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  padding: 30px;
  border-radius: 8px;
  max-width: 500px;
  width: 90%;
  position: relative;
}

.close-btn {
  position: absolute;
  top: 10px;
  right: 10px;
  border: none;
  background: none;
  font-size: 24px;
  cursor: pointer;
}
</style>
```

**Using the Modal:**

```vue
<template>
  <div>
    <button @click="showModal = true">Open Modal</button>
    
    <Modal :is-open="showModal" @close="showModal = false">
      <h2>Modal Title</h2>
      <p>Modal content goes here</p>
    </Modal>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import Modal from './components/Modal.vue';

const showModal = ref(false);
</script>
```

### Teleport to Different Targets

```vue
<template>
  <!-- Teleport to body -->
  <teleport to="body">
    <div class="notification">Notification</div>
  </teleport>
  
  <!-- Teleport to specific element -->
  <teleport to="#modal-root">
    <div class="modal">Modal</div>
  </teleport>
  
  <!-- Conditional teleport -->
  <teleport to="body" :disabled="!isMobile">
    <div class="sidebar">Sidebar</div>
  </teleport>
</template>

<script setup>
import { ref } from 'vue';
const isMobile = ref(window.innerWidth < 768);
</script>
```

---

## 24. Transitions & Animations

Vue provides built-in transition components for animating elements.

### Basic Transition

```vue
<template>
  <button @click="show = !show">Toggle</button>
  
  <transition name="fade">
    <p v-if="show">Hello Vue!</p>
  </transition>
</template>

<script setup>
import { ref } from 'vue';
const show = ref(true);
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
```

### Transition Classes

Vue applies these classes automatically:

```
v-enter-from    → Element is inserted
v-enter-active  → During enter transition
v-enter-to      → Enter transition finished

v-leave-from    → Leave starts
v-leave-active  → During leave transition
v-leave-to      → Leave finished
```

### Slide Transition

```vue
<template>
  <button @click="show = !show">Toggle</button>
  
  <transition name="slide">
    <div v-if="show" class="box">
      Sliding content
    </div>
  </transition>
</template>

<script setup>
import { ref } from 'vue';
const show = ref(true);
</script>

<style>
.slide-enter-active,
.slide-leave-active {
  transition: all 0.3s ease;
}

.slide-enter-from {
  transform: translateX(-100%);
  opacity: 0;
}

.slide-leave-to {
  transform: translateX(100%);
  opacity: 0;
}

.box {
  padding: 20px;
  background: #42b983;
  color: white;
}
</style>
```

### List Transitions

```vue
<template>
  <button @click="addItem">Add Item</button>
  
  <transition-group name="list" tag="ul">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
      <button @click="removeItem(item.id)">×</button>
    </li>
  </transition-group>
</template>

<script setup>
import { ref } from 'vue';

const items = ref([
  { id: 1, text: 'Item 1' },
  { id: 2, text: 'Item 2' }
]);

let nextId = 3;

function addItem() {
  items.value.push({
    id: nextId++,
    text: `Item ${nextId - 1}`
  });
}

function removeItem(id) {
  items.value = items.value.filter(item => item.id !== id);
}
</script>

<style>
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

.list-move {
  transition: transform 0.5s;
}
</style>
```

### Using CSS Animation Libraries

**With Animate.css:**

```bash
npm install animate.css
```

```vue
<template>
  <transition
    enter-active-class="animate__animated animate__bounceIn"
    leave-active-class="animate__animated animate__bounceOut"
  >
    <p v-if="show">Animated with Animate.css</p>
  </transition>
</template>

<script setup>
import { ref } from 'vue';
import 'animate.css';

const show = ref(true);
</script>
```

### JavaScript Hooks

```vue
<template>
  <transition
    @before-enter="beforeEnter"
    @enter="enter"
    @after-enter="afterEnter"
    @before-leave="beforeLeave"
    @leave="leave"
    @after-leave="afterLeave"
  >
    <div v-if="show">Element</div>
  </transition>
</template>

<script setup>
import { ref } from 'vue';

const show = ref(true);

function beforeEnter(el) {
  el.style.opacity = 0;
}

function enter(el, done) {
  el.offsetHeight; // Trigger reflow
  el.style.transition = 'opacity 0.5s';
  el.style.opacity = 1;
  done();
}

function afterEnter(el) {
  console.log('Enter complete');
}

function beforeLeave(el) {
  el.style.opacity = 1;
}

function leave(el, done) {
  el.style.transition = 'opacity 0.5s';
  el.style.opacity = 0;
  setTimeout(done, 500);
}

function afterLeave(el) {
  console.log('Leave complete');
}
</script>
```

**Transition Documentation:** https://vuejs.org/guide/built-ins/transition.html

---

## 25. Best Practices

### 1. Component Organization

```
src/
├── components/
│   ├── common/          # Reusable components
│   │   ├── Button.vue
│   │   └── Input.vue
│   ├── layout/          # Layout components
│   │   ├── Header.vue
│   │   └── Footer.vue
│   └── features/        # Feature-specific
│       └── UserProfile.vue
├── views/               # Page components
├── composables/         # Reusable logic
├── stores/              # Pinia stores
├── router/              # Router config
└── assets/              # Static files
```

### 2. Naming Conventions

```vue
<!-- PascalCase for component names -->
<template>
  <UserProfile />
  <TodoList />
</template>

<!-- kebab-case for props and events -->
<UserCard 
  :user-name="name"
  @update-profile="handleUpdate"
/>
```

### 3. Use Composition API

```vue
<!-- ✅ Good -->
<script setup>
import { ref, computed } from 'vue';

const count = ref(0);
const double = computed(() => count.value * 2);
</script>

<!-- ❌ Avoid Options API for new projects -->
<script>
export default {
  data() {
    return { count: 0 };
  }
};
</script>
```

### 4. Keep Components Small

```vue
<!-- ✅ Good: Small, focused component -->
<template>
  <button @click="handleClick">
    {{ label }}
  </button>
</template>

<script setup>
defineProps(['label']);
const emit = defineEmits(['click']);

function handleClick() {
  emit('click');
}
</script>

<!-- ❌ Avoid: Too much logic in one component -->
```

### 5. Use Props Validation

```vue
<script setup>
// ✅ Good: With validation
defineProps({
  title: {
    type: String,
    required: true
  },
  age: {
    type: Number,
    default: 0,
    validator: (value) => value >= 0
  }
});

// ❌ Avoid: No validation
defineProps(['title', 'age']);
</script>
```

### 6. Use Computed for Derived State

```vue
<script setup>
import { ref, computed } from 'vue';

const items = ref([1, 2, 3, 4, 5]);

// ✅ Good: Use computed
const evenItems = computed(() => {
  return items.value.filter(item => item % 2 === 0);
});

// ❌ Avoid: Calculating in template
// <div v-for="item in items.filter(i => i % 2 === 0)">
</script>
```

### 7. Use Key with v-for

```vue
<!-- ✅ Good -->
<li v-for="item in items" :key="item.id">
  {{ item.name }}
</li>

<!-- ❌ Avoid: Using index as key -->
<li v-for="(item, index) in items" :key="index">
  {{ item.name }}
</li>
```

### 8. Extract Reusable Logic to Composables

```js
// composables/useLocalStorage.js
import { ref, watch } from 'vue';

export function useLocalStorage(key, defaultValue) {
  const value = ref(
    JSON.parse(localStorage.getItem(key)) || defaultValue
  );
  
  watch(value, (newValue) => {
    localStorage.setItem(key, JSON.stringify(newValue));
  }, { deep: true });
  
  return value;
}
```

### 9. Use Async/Await for API Calls

```vue
<script setup>
// ✅ Good
async function fetchData() {
  try {
    const response = await axios.get('/api/data');
    data.value = response.data;
  } catch (error) {
    console.error(error);
  }
}

// ❌ Avoid: Promise chains
function fetchData() {
  axios.get('/api/data')
    .then(response => data.value = response.data)
    .catch(error => console.error(error));
}
</script>
```

### 10. Error Handling

```vue
<script setup>
import { ref } from 'vue';

const error = ref(null);
const loading = ref(false);

async function fetchData() {
  loading.value = true;
  error.value = null;
  
  try {
    // API call
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
}
</script>

<template>
  <div v-if="loading">Loading...</div>
  <div v-else-if="error">Error: {{ error }}</div>
  <div v-else>{{ data }}</div>
</template>
```

### 11. Use Environment Variables

```js
// .env
VITE_API_URL=https://api.example.com
VITE_APP_TITLE=My App

// Use in code
const apiUrl = import.meta.env.VITE_API_URL;
```

### 12. Lazy Load Routes

```js
// router/index.js
const routes = [
  {
    path: '/about',
    // ✅ Good: Lazy load
    component: () => import('../views/About.vue')
  },
  {
    path: '/home',
    // ❌ Avoid: Import all at once
    component: Home
  }
];
```

---

## 26. Building for Production

### Build Your App

```bash
npm run build
```

This creates a `dist/` folder with optimized files.

### Build Output

```
dist/
├── index.html
├── assets/
│   ├── index.[hash].js
│   ├── index.[hash].css
│   └── logo.[hash].png
└── favicon.ico
```

### Environment Variables

Create environment files:

```bash
# .env.production
VITE_API_URL=https://api.production.com

# .env.development
VITE_API_URL=http://localhost:3000
```

### Optimization Tips

**1. Code Splitting:**
```js
// Use dynamic imports
const UserProfile = () => import('./components/UserProfile.vue');
```

**2. Tree Shaking:**
```js
// Import only what you need
import { ref, computed } from 'vue';
// Instead of: import * as Vue from 'vue';
```

**3. Compress Images:**
- Use WebP format
- Compress before deployment
- Use lazy loading for images

**4. Enable Gzip/Brotli:**
Most hosting providers enable this automatically.

### Deployment Options

**Vercel:**
```bash
npm install -g vercel
vercel
```

**Netlify:**
```bash
npm install -g netlify-cli
netlify deploy --prod
```

**GitHub Pages:**
```js
// vite.config.js
export default {
  base: '/your-repo-name/'
}
```

```bash
npm run build
git add dist -f
git commit -m "Deploy"
git subtree push --prefix dist origin gh-pages
```

**Cloudflare Pages:**
1. Connect GitHub repo
2. Set build command: `npm run build`
3. Set output directory: `dist`

**Docker:**
```dockerfile
FROM node:18-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Performance Checklist

✅ Enable production mode  
✅ Minify CSS and JavaScript  
✅ Optimize images  
✅ Use lazy loading  
✅ Enable caching  
✅ Use CDN for static assets  
✅ Compress files (Gzip/Brotli)  
✅ Remove console.logs  
✅ Analyze bundle size

### Analyze Bundle Size

```bash
npm run build -- --report
```

Or use:
```bash
npm install -D rollup-plugin-visualizer
```

```js
// vite.config.js
import { visualizer } from 'rollup-plugin-visualizer';

export default {
  plugins: [
    visualizer({
      open: true
    })
  ]
}
```

---

## 27. Useful Resources

### Official Documentation
- **Vue.js:** https://vuejs.org
- **Vue Router:** https://router.vuejs.org
- **Pinia:** https://pinia.vuejs.org
- **Vite:** https://vitejs.dev
- **Composition API:** https://vuejs.org/guide/extras/composition-api-faq.html

### Tools & Libraries
- **Axios:** https://axios-http.com
- **VueUse:** https://vueuse.org (Collection of Vue composables)
- **Vitest:** https://vitest.dev (Testing framework)
- **Vue Devtools:** Browser extension for debugging

### UI Component Libraries
- **Vuetify:** https://vuetifyjs.com
- **Element Plus:** https://element-plus.org
- **Quasar:** https://quasar.dev
- **PrimeVue:** https://primevue.org
- **Ant Design Vue:** https://antdv.com
- **Naive UI:** https://naiveui.com

### CSS Frameworks
- **Tailwind CSS:** https://tailwindcss.com
- **Bootstrap Vue:** https://bootstrap-vue.org
- **UnoCSS:** https://unocss.dev

### Learning Resources
- **Vue Mastery:** https://vuemastery.com
- **Vue School:** https://vueschool.io
- **Official Tutorial:** https://vuejs.org/tutorial/
- **Awesome Vue:** https://github.com/vuejs/awesome-vue

### Community
- **Vue Forum:** https://forum.vuejs.org
- **Discord:** https://discord.com/invite/vue
- **Twitter:** @vuejs
- **Reddit:** r/vuejs

### VS Code Extensions
- **Volar** - Vue Language Features
- **Vue VSCode Snippets**
- **ESLint**
- **Prettier**

### API Testing
- **JSONPlaceholder:** https://jsonplaceholder.typicode.com (Fake REST API)
- **ReqRes:** https://reqres.in (Test API)

### Deployment Platforms
- **Vercel:** https://vercel.com
- **Netlify:** https://netlify.com
- **Cloudflare Pages:** https://pages.cloudflare.com
- **GitHub Pages:** https://pages.github.com
- **Railway:** https://railway.app
- **Render:** https://render.com

---

## Quick Reference Card

### Creating Reactive Data
```js
import { ref, reactive } from 'vue';

const count = ref(0);                    // Primitive
const user = reactive({ name: 'John' }); // Object
```

### Computed & Watch
```js
import { computed, watch } from 'vue';

const double = computed(() => count.value * 2);
watch(count, (newVal) => console.log(newVal));
```

### Directives
```html
<p v-if="show">Conditional</p>
<p v-show="visible">Toggle visibility</p>
<li v-for="item in items" :key="item.id">{{ item }}</li>
<input v-model="text">
<button @click="handleClick">Click</button>
<img :src="imageUrl">
```

### Component Communication
```js
// Props
defineProps({ title: String });

// Emits
const emit = defineEmits(['update']);
emit('update', value);
```

### Lifecycle Hooks
```js
import { onMounted, onUnmounted } from 'vue';

onMounted(() => console.log('Mounted'));
onUnmounted(() => console.log('Cleanup'));
```

### Router
```js
import { useRouter, useRoute } from 'vue-router';

const router = useRouter();
const route = useRoute();

router.push('/path');
const id = route.params.id;
```

### Store (Pinia)
```js
import { defineStore } from 'pinia';

export const useStore = defineStore('main', () => {
  const count = ref(0);
  function increment() { count.value++; }
  return { count, increment };
});
```

---

## Congratulations! 🎉

You've completed the Vue.js beginner's guide! You now have a solid foundation in:

✅ Vue fundamentals  
✅ Components and composition  
✅ State management  
✅ Routing  
✅ API integration  
✅ Best practices  

### Next Steps:
1. Build a small project (Todo App, Weather App, etc.)
2. Explore advanced topics (SSR, Nuxt.js)
3. Join the Vue community
4. Contribute to open-source Vue projects
5. Keep practicing and building!

**Happy Coding with Vue! 💚**

---

*Document Version: 1.0*  
*Last Updated: December 2024*  
*Vue Version: 3.x*