# Complete Bootstrap Tutorial: From Zero to Hero

## Table of Contents
1. [Introduction to Bootstrap](#introduction)
2. [Getting Started](#getting-started)
3. [Layout System](#layout-system)
4. [Components](#components)
5. [Utilities](#utilities)
6. [Advanced Techniques](#advanced-techniques)
7. [Real-World Projects](#real-world-projects)

---

## Introduction to Bootstrap

Bootstrap is the world's most popular front-end framework for building responsive, mobile-first websites. It provides pre-built CSS classes and JavaScript components that speed up development.

### Why Bootstrap?
- **Responsive Grid System**: Built-in 12-column grid that adapts to all screen sizes
- **Pre-styled Components**: Buttons, forms, navbars, cards, and more
- **Cross-browser Compatibility**: Works consistently across all modern browsers
- **Large Community**: Extensive documentation and community support
- **Customizable**: Easy to customize using Sass variables

---

## Getting Started

### Installation Methods

#### Method 1: CDN (Fastest)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bootstrap Tutorial</title>
    
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    
    <h1>Hello, Bootstrap!</h1>
    
    <!-- Bootstrap JavaScript Bundle (includes Popper) -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

#### Method 2: NPM
```bash
npm install bootstrap@5.3.2
```

#### Method 3: Download
Download from [getbootstrap.com](https://getbootstrap.com/) and include locally.

### Essential Meta Tag
Always include this viewport meta tag for responsive behavior:
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

---

## Layout System

### The Container

Containers are the most basic layout element and are required when using the grid system.

```html
<!-- Fixed-width container -->
<div class="container">
    Content here
</div>

<!-- Full-width container -->
<div class="container-fluid">
    Content here
</div>

<!-- Responsive containers -->
<div class="container-sm">100% wide until sm breakpoint</div>
<div class="container-md">100% wide until md breakpoint</div>
<div class="container-lg">100% wide until lg breakpoint</div>
<div class="container-xl">100% wide until xl breakpoint</div>
<div class="container-xxl">100% wide until xxl breakpoint</div>
```

### The Grid System

Bootstrap uses a 12-column flexbox grid system.

#### Breakpoints
- **xs**: <576px (default, no class prefix)
- **sm**: ≥576px (class prefix: `sm`)
- **md**: ≥768px (class prefix: `md`)
- **lg**: ≥992px (class prefix: `lg`)
- **xl**: ≥1200px (class prefix: `xl`)
- **xxl**: ≥1400px (class prefix: `xxl`)

#### Basic Grid Example
```html
<div class="container">
    <div class="row">
        <div class="col">Column 1</div>
        <div class="col">Column 2</div>
        <div class="col">Column 3</div>
    </div>
</div>
```

#### Responsive Grid
```html
<div class="container">
    <div class="row">
        <!-- Stack on mobile, 2 columns on tablet, 3 on desktop -->
        <div class="col-12 col-md-6 col-lg-4">Column 1</div>
        <div class="col-12 col-md-6 col-lg-4">Column 2</div>
        <div class="col-12 col-md-6 col-lg-4">Column 3</div>
    </div>
</div>
```

#### Column Sizing
```html
<div class="row">
    <div class="col-4">4 columns wide</div>
    <div class="col-8">8 columns wide</div>
</div>

<div class="row">
    <div class="col-6">6 columns</div>
    <div class="col-6">6 columns</div>
</div>
```

#### Column Alignment
```html
<!-- Vertical alignment -->
<div class="row align-items-start">...</div>
<div class="row align-items-center">...</div>
<div class="row align-items-end">...</div>

<!-- Horizontal alignment -->
<div class="row justify-content-start">...</div>
<div class="row justify-content-center">...</div>
<div class="row justify-content-end">...</div>
<div class="row justify-content-between">...</div>
<div class="row justify-content-around">...</div>
```

#### Column Ordering
```html
<div class="row">
    <div class="col order-3">First in DOM, third visually</div>
    <div class="col order-1">Second in DOM, first visually</div>
    <div class="col order-2">Third in DOM, second visually</div>
</div>
```

#### Offsetting Columns
```html
<div class="row">
    <div class="col-md-4 offset-md-4">Centered 4-column</div>
</div>

<div class="row">
    <div class="col-md-3 offset-md-3">Content</div>
    <div class="col-md-3 offset-md-3">Content</div>
</div>
```

#### Gutters (Spacing between columns)
```html
<!-- Default gutters -->
<div class="row">
    <div class="col">Column</div>
    <div class="col">Column</div>
</div>

<!-- Custom gutters -->
<div class="row g-0">No gutters</div>
<div class="row g-2">Small gutters</div>
<div class="row g-3">Medium gutters</div>
<div class="row g-5">Large gutters</div>

<!-- Horizontal and vertical gutters -->
<div class="row gx-3 gy-4">Different horizontal and vertical spacing</div>
```

---

## Components

### Buttons

```html
<!-- Button styles -->
<button class="btn btn-primary">Primary</button>
<button class="btn btn-secondary">Secondary</button>
<button class="btn btn-success">Success</button>
<button class="btn btn-danger">Danger</button>
<button class="btn btn-warning">Warning</button>
<button class="btn btn-info">Info</button>
<button class="btn btn-light">Light</button>
<button class="btn btn-dark">Dark</button>
<button class="btn btn-link">Link</button>

<!-- Outline buttons -->
<button class="btn btn-outline-primary">Outline Primary</button>
<button class="btn btn-outline-success">Outline Success</button>

<!-- Button sizes -->
<button class="btn btn-primary btn-lg">Large Button</button>
<button class="btn btn-primary">Normal Button</button>
<button class="btn btn-primary btn-sm">Small Button</button>

<!-- Block buttons (full width) -->
<button class="btn btn-primary w-100">Block Button</button>

<!-- Disabled buttons -->
<button class="btn btn-primary" disabled>Disabled</button>
```

### Navbar

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container-fluid">
        <a class="navbar-brand" href="#">Brand</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item">
                    <a class="nav-link active" href="#">Home</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Features</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Pricing</a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">
                        Dropdown
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Action</a></li>
                        <li><a class="dropdown-item" href="#">Another action</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Something else</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
```

### Cards

```html
<!-- Basic card -->
<div class="card" style="width: 18rem;">
    <img src="image.jpg" class="card-img-top" alt="Image">
    <div class="card-body">
        <h5 class="card-title">Card Title</h5>
        <p class="card-text">Some quick example text to build on the card title.</p>
        <a href="#" class="btn btn-primary">Go somewhere</a>
    </div>
</div>

<!-- Card with header and footer -->
<div class="card">
    <div class="card-header">
        Featured
    </div>
    <div class="card-body">
        <h5 class="card-title">Special title treatment</h5>
        <p class="card-text">With supporting text below.</p>
        <a href="#" class="btn btn-primary">Button</a>
    </div>
    <div class="card-footer text-muted">
        2 days ago
    </div>
</div>

<!-- Card groups (equal height) -->
<div class="card-group">
    <div class="card">
        <img src="..." class="card-img-top" alt="...">
        <div class="card-body">
            <h5 class="card-title">Card 1</h5>
            <p class="card-text">Content</p>
        </div>
    </div>
    <div class="card">
        <img src="..." class="card-img-top" alt="...">
        <div class="card-body">
            <h5 class="card-title">Card 2</h5>
            <p class="card-text">Content</p>
        </div>
    </div>
</div>
```

### Forms

```html
<form>
    <!-- Basic input -->
    <div class="mb-3">
        <label for="email" class="form-label">Email address</label>
        <input type="email" class="form-control" id="email" placeholder="name@example.com">
    </div>
    
    <!-- Textarea -->
    <div class="mb-3">
        <label for="message" class="form-label">Message</label>
        <textarea class="form-control" id="message" rows="3"></textarea>
    </div>
    
    <!-- Select -->
    <div class="mb-3">
        <label for="country" class="form-label">Country</label>
        <select class="form-select" id="country">
            <option selected>Choose...</option>
            <option value="1">USA</option>
            <option value="2">Canada</option>
            <option value="3">UK</option>
        </select>
    </div>
    
    <!-- Checkboxes -->
    <div class="mb-3">
        <div class="form-check">
            <input class="form-check-input" type="checkbox" id="check1">
            <label class="form-check-label" for="check1">
                Remember me
            </label>
        </div>
    </div>
    
    <!-- Radio buttons -->
    <div class="mb-3">
        <div class="form-check">
            <input class="form-check-input" type="radio" name="radio" id="radio1">
            <label class="form-check-label" for="radio1">
                Option 1
            </label>
        </div>
        <div class="form-check">
            <input class="form-check-input" type="radio" name="radio" id="radio2">
            <label class="form-check-label" for="radio2">
                Option 2
            </label>
        </div>
    </div>
    
    <!-- Input groups -->
    <div class="input-group mb-3">
        <span class="input-group-text">@</span>
        <input type="text" class="form-control" placeholder="Username">
    </div>
    
    <!-- Validation states -->
    <div class="mb-3">
        <input type="text" class="form-control is-valid" placeholder="Valid input">
        <div class="valid-feedback">Looks good!</div>
    </div>
    
    <div class="mb-3">
        <input type="text" class="form-control is-invalid" placeholder="Invalid input">
        <div class="invalid-feedback">Please provide a valid input.</div>
    </div>
    
    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

### Modals

```html
<!-- Button trigger -->
<button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#myModal">
    Launch Modal
</button>

<!-- Modal -->
<div class="modal fade" id="myModal" tabindex="-1">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Modal Title</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
                <p>Modal body text goes here.</p>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                <button type="button" class="btn btn-primary">Save changes</button>
            </div>
        </div>
    </div>
</div>

<!-- Modal sizes -->
<div class="modal-dialog modal-sm">Small modal</div>
<div class="modal-dialog modal-lg">Large modal</div>
<div class="modal-dialog modal-xl">Extra large modal</div>

<!-- Fullscreen modal -->
<div class="modal-dialog modal-fullscreen">Fullscreen modal</div>

<!-- Vertically centered -->
<div class="modal-dialog modal-dialog-centered">Centered modal</div>

<!-- Scrollable modal -->
<div class="modal-dialog modal-dialog-scrollable">Scrollable modal</div>
```

### Alerts

```html
<div class="alert alert-primary" role="alert">
    Primary alert
</div>
<div class="alert alert-success" role="alert">
    Success alert
</div>
<div class="alert alert-danger" role="alert">
    Danger alert
</div>

<!-- Alert with icon and dismissible -->
<div class="alert alert-warning alert-dismissible fade show" role="alert">
    <strong>Warning!</strong> You should check in on some of those fields below.
    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
</div>

<!-- Alert with link -->
<div class="alert alert-info" role="alert">
    This is an info alert with <a href="#" class="alert-link">a link</a>.
</div>
```

### Badges

```html
<h1>Example heading <span class="badge bg-secondary">New</span></h1>

<button type="button" class="btn btn-primary">
    Notifications <span class="badge bg-danger">4</span>
</button>

<!-- Pill badges -->
<span class="badge rounded-pill bg-primary">Primary</span>
<span class="badge rounded-pill bg-success">Success</span>
```

### Breadcrumbs

```html
<nav aria-label="breadcrumb">
    <ol class="breadcrumb">
        <li class="breadcrumb-item"><a href="#">Home</a></li>
        <li class="breadcrumb-item"><a href="#">Library</a></li>
        <li class="breadcrumb-item active">Data</li>
    </ol>
</nav>
```

### Pagination

```html
<nav>
    <ul class="pagination">
        <li class="page-item disabled">
            <a class="page-link" href="#">Previous</a>
        </li>
        <li class="page-item"><a class="page-link" href="#">1</a></li>
        <li class="page-item active"><a class="page-link" href="#">2</a></li>
        <li class="page-item"><a class="page-link" href="#">3</a></li>
        <li class="page-item">
            <a class="page-link" href="#">Next</a>
        </li>
    </ul>
</nav>

<!-- Pagination sizes -->
<ul class="pagination pagination-lg">...</ul>
<ul class="pagination pagination-sm">...</ul>
```

### Carousel

```html
<div id="carouselExample" class="carousel slide" data-bs-ride="carousel">
    <div class="carousel-indicators">
        <button type="button" data-bs-target="#carouselExample" data-bs-slide-to="0" class="active"></button>
        <button type="button" data-bs-target="#carouselExample" data-bs-slide-to="1"></button>
        <button type="button" data-bs-target="#carouselExample" data-bs-slide-to="2"></button>
    </div>
    
    <div class="carousel-inner">
        <div class="carousel-item active">
            <img src="image1.jpg" class="d-block w-100" alt="Slide 1">
            <div class="carousel-caption d-none d-md-block">
                <h5>First slide label</h5>
                <p>Description for first slide.</p>
            </div>
        </div>
        <div class="carousel-item">
            <img src="image2.jpg" class="d-block w-100" alt="Slide 2">
        </div>
        <div class="carousel-item">
            <img src="image3.jpg" class="d-block w-100" alt="Slide 3">
        </div>
    </div>
    
    <button class="carousel-control-prev" type="button" data-bs-target="#carouselExample" data-bs-slide="prev">
        <span class="carousel-control-prev-icon"></span>
    </button>
    <button class="carousel-control-next" type="button" data-bs-target="#carouselExample" data-bs-slide="next">
        <span class="carousel-control-next-icon"></span>
    </button>
</div>
```

### Accordion

```html
<div class="accordion" id="accordionExample">
    <div class="accordion-item">
        <h2 class="accordion-header">
            <button class="accordion-button" type="button" data-bs-toggle="collapse" data-bs-target="#collapseOne">
                Accordion Item #1
            </button>
        </h2>
        <div id="collapseOne" class="accordion-collapse collapse show" data-bs-parent="#accordionExample">
            <div class="accordion-body">
                Content for accordion item 1.
            </div>
        </div>
    </div>
    
    <div class="accordion-item">
        <h2 class="accordion-header">
            <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseTwo">
                Accordion Item #2
            </button>
        </h2>
        <div id="collapseTwo" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
            <div class="accordion-body">
                Content for accordion item 2.
            </div>
        </div>
    </div>
</div>
```

### Tooltips and Popovers

```html
<!-- Tooltip -->
<button type="button" class="btn btn-secondary" 
        data-bs-toggle="tooltip" 
        data-bs-placement="top" 
        title="Tooltip text">
    Hover for tooltip
</button>

<!-- Popover -->
<button type="button" class="btn btn-danger" 
        data-bs-toggle="popover" 
        data-bs-title="Popover title"
        data-bs-content="Popover content goes here.">
    Click for popover
</button>

<script>
// Initialize tooltips
var tooltipTriggerList = [].slice.call(document.querySelectorAll('[data-bs-toggle="tooltip"]'))
var tooltipList = tooltipTriggerList.map(function (tooltipTriggerEl) {
    return new bootstrap.Tooltip(tooltipTriggerEl)
})

// Initialize popovers
var popoverTriggerList = [].slice.call(document.querySelectorAll('[data-bs-toggle="popover"]'))
var popoverList = popoverTriggerList.map(function (popoverTriggerEl) {
    return new bootstrap.Popover(popoverTriggerEl)
})
</script>
```

### Progress Bars

```html
<!-- Basic progress bar -->
<div class="progress">
    <div class="progress-bar" style="width: 25%">25%</div>
</div>

<!-- Colored progress bars -->
<div class="progress">
    <div class="progress-bar bg-success" style="width: 40%"></div>
</div>

<!-- Striped -->
<div class="progress">
    <div class="progress-bar progress-bar-striped" style="width: 60%"></div>
</div>

<!-- Animated -->
<div class="progress">
    <div class="progress-bar progress-bar-striped progress-bar-animated" style="width: 75%"></div>
</div>

<!-- Multiple bars -->
<div class="progress">
    <div class="progress-bar bg-success" style="width: 15%"></div>
    <div class="progress-bar bg-warning" style="width: 30%"></div>
    <div class="progress-bar bg-danger" style="width: 20%"></div>
</div>
```

### Spinners

```html
<!-- Border spinner -->
<div class="spinner-border" role="status">
    <span class="visually-hidden">Loading...</span>
</div>

<!-- Colored spinners -->
<div class="spinner-border text-primary" role="status"></div>
<div class="spinner-border text-success" role="status"></div>

<!-- Growing spinner -->
<div class="spinner-grow text-danger" role="status"></div>

<!-- Spinner sizes -->
<div class="spinner-border spinner-border-sm" role="status"></div>

<!-- Spinner in button -->
<button class="btn btn-primary" type="button" disabled>
    <span class="spinner-border spinner-border-sm" role="status"></span>
    Loading...
</button>
```

### Toasts

```html
<div class="toast-container position-fixed bottom-0 end-0 p-3">
    <div class="toast" role="alert" id="myToast">
        <div class="toast-header">
            <strong class="me-auto">Bootstrap</strong>
            <small>11 mins ago</small>
            <button type="button" class="btn-close" data-bs-dismiss="toast"></button>
        </div>
        <div class="toast-body">
            Hello, world! This is a toast message.
        </div>
    </div>
</div>

<script>
// Show toast
var myToast = new bootstrap.Toast(document.getElementById('myToast'))
myToast.show()
</script>
```

---

## Utilities

### Spacing

Bootstrap uses a shorthand for margin and padding: `{property}{sides}-{size}`

**Properties:**
- `m` - margin
- `p` - padding

**Sides:**
- `t` - top
- `b` - bottom
- `s` - start (left in LTR)
- `e` - end (right in LTR)
- `x` - left and right
- `y` - top and bottom
- blank - all sides

**Sizes:** 0, 1, 2, 3, 4, 5, auto

```html
<div class="mt-3">Margin top 3</div>
<div class="p-5">Padding all sides 5</div>
<div class="mx-auto">Margin left and right auto (center)</div>
<div class="px-3 py-2">Padding horizontal 3, vertical 2</div>
```

### Display

```html
<div class="d-none">Hidden on all</div>
<div class="d-block">Block on all</div>
<div class="d-inline">Inline on all</div>
<div class="d-inline-block">Inline-block on all</div>
<div class="d-flex">Flexbox on all</div>
<div class="d-grid">Grid on all</div>

<!-- Responsive display -->
<div class="d-none d-md-block">Hidden on mobile, visible on tablet+</div>
<div class="d-block d-lg-none">Visible on mobile/tablet, hidden on desktop</div>
```

### Flexbox

```html
<!-- Flex direction -->
<div class="d-flex flex-row">Horizontal</div>
<div class="d-flex flex-column">Vertical</div>

<!-- Justify content -->
<div class="d-flex justify-content-start">Start</div>
<div class="d-flex justify-content-center">Center</div>
<div class="d-flex justify-content-end">End</div>
<div class="d-flex justify-content-between">Space between</div>
<div class="d-flex justify-content-around">Space around</div>

<!-- Align items -->
<div class="d-flex align-items-start">Start</div>
<div class="d-flex align-items-center">Center</div>
<div class="d-flex align-items-end">End</div>

<!-- Flex wrap -->
<div class="d-flex flex-wrap">Wrap</div>
<div class="d-flex flex-nowrap">No wrap</div>

<!-- Flex grow and shrink -->
<div class="flex-grow-1">Grows to fill space</div>
<div class="flex-shrink-1">Shrinks if needed</div>
```

### Colors

```html
<!-- Text colors -->
<p class="text-primary">Primary text</p>
<p class="text-success">Success text</p>
<p class="text-danger">Danger text</p>
<p class="text-warning">Warning text</p>
<p class="text-info">Info text</p>
<p class="text-muted">Muted text</p>
<p class="text-white">White text</p>
<p class="text-black">Black text</p>

<!-- Background colors -->
<div class="bg-primary text-white">Primary background</div>
<div class="bg-success text-white">Success background</div>
<div class="bg-danger text-white">Danger background</div>
<div class="bg-light">Light background</div>
<div class="bg-dark text-white">Dark background</div>
```

### Text Utilities

```html
<!-- Alignment -->
<p class="text-start">Left aligned text</p>
<p class="text-center">Center aligned text</p>
<p class="text-end">Right aligned text</p>

<!-- Text transform -->
<p class="text-lowercase">LOWERCASED TEXT</p>
<p class="text-uppercase">uppercased text</p>
<p class="text-capitalize">capitalized text</p>

<!-- Font weight and style -->
<p class="fw-bold">Bold text</p>
<p class="fw-normal">Normal weight text</p>
<p class="fw-light">Light weight text</p>
<p class="fst-italic">Italic text</p>

<!-- Font size -->
<p class="fs-1">Font size 1 (largest)</p>
<p class="fs-2">Font size 2</p>
<p class="fs-3">Font size 3</p>
<p class="fs-4">Font size 4</p>
<p class="fs-5">Font size 5</p>
<p class="fs-6">Font size 6 (smallest)</p>

<!-- Text decoration -->
<p class="text-decoration-none">No decoration</p>
<p class="text-decoration-underline">Underlined</p>
<p class="text-decoration-line-through">Line through</p>
```

### Borders

```html
<!-- Border -->
<div class="border">All borders</div>
<div class="border-top">Top border</div>
<div class="border-end">End border</div>
<div class="border-bottom">Bottom border</div>
<div class="border-start">Start border</div>

<!-- Border colors -->
<div class="border border-primary">Primary border</div>
<div class="border border-success">Success border</div>

<!-- Border width -->
<div class="border border-1">Border 1</div>
<div class="border border-2">Border 2</div>
<div class="border border-3">Border 3</div>
<div class="border border-4">Border 4</div>
<div class="border border-5">Border 5</div>

<!-- Border radius -->
<div class="rounded">Rounded</div>
<div class="rounded-top">Rounded top</div>
<div class="rounded-circle">Circle (use with square elements)</div>
<div class="rounded-pill">Pill shape</div>
```

### Sizing

```html
<!-- Width -->
<div class="w-25">Width 25%</div>
<div class="w-50">Width 50%</div>
<div class="w-75">Width 75%</div>
<div class="w-100">Width 100%</div>
<div class="w-auto">Width auto</div>

<!-- Height -->
<div class="h-25">Height 25%</div>
<div class="h-50">Height 50%</div>
<div class="h-75">Height 75%</div>
<div class="h-100">Height 100%</div>

<!-- Max width/height -->
<div class="mw-100">Max width 100%</div>
<div class="mh-100">Max height 100%</div>

<!-- Viewport sizing -->
<div class="vw-100">Width 100vw</div>
<div class="vh-100">Height 100vh</div>
```

### Position

```html
<div class="position-static">Static</div>
<div class="position-relative">Relative</div>
<div class="position-absolute">Absolute</div>
<div class="position-fixed">Fixed</div>
<div class="position-sticky">Sticky</div>

<!-- Position helpers -->
<div class="position-relative">
    <div class="position-absolute top-0 start-0">Top left</div>
    <div class="position-absolute top-0 end-0">Top right</div>
    <div class="position-absolute bottom-0 start-0">Bottom left</div>
    <div class="position-absolute bottom-0 end-0">Bottom right</div>
</div>

<!-- Translate middle -->
<div class="position-absolute top-50 start-50 translate-middle">Centered</div>
```

### Shadow

```html
<div class="shadow-none">No shadow</div>
<div class="shadow-sm">Small shadow</div>
<div class="shadow">Regular shadow</div>
<div class="shadow-lg">Large shadow</div>
```

### Visibility

```html
<div class="visible">Visible</div>
<div class="invisible">Invisible (takes space)</div>
```

### Overflow

```html
<div class="overflow-auto">Auto overflow</div>
<div class="overflow-hidden">Hidden overflow</div>
<div class="overflow-visible">Visible overflow</div>
<div class="overflow-scroll">Scroll overflow</div>
```

---

## Advanced Techniques

### Customizing Bootstrap with CSS

Override Bootstrap variables by creating your own CSS:

```css
/* custom.css */
:root {
    --bs-primary: #ff6b6b;
    --bs-secondary: #4ecdc4;
    --bs-success: #95e1d3;
    --bs-font-sans-serif: 'Inter', system-ui, -apple-system, sans-serif;
}

/* Custom component styles */
.navbar-custom {
    background: linear-gradient(135deg, var(--bs-primary) 0%, var(--bs-secondary) 100%);
}

.btn-custom {
    background-color: #ff6b6b;
    border-color: #ff6b6b;
    color: white;
}

.btn-custom:hover {
    background-color: #ff5252;
    border-color: #ff5252;
}
```

### Using Sass Variables (Advanced)

If you're using Bootstrap source files with Sass:

```scss
// custom.scss

// Override default variables before importing Bootstrap
$primary: #ff6b6b;
$secondary: #4ecdc4;
$success: #95e1d3;
$danger: #f38181;
$font-family-base: 'Inter', system-ui, -apple-system, sans-serif;

// Change spacing scale
$spacer: 1rem;
$spacers: (
  0: 0,
  1: $spacer * .25,
  2: $spacer * .5,
  3: $spacer,
  4: $spacer * 1.5,
  5: $spacer * 3,
  6: $spacer * 4.5,
  7: $spacer * 6
);

// Import Bootstrap
@import "bootstrap/scss/bootstrap";

// Add custom styles after Bootstrap import
.custom-hero {
  background: linear-gradient(135deg, $primary 0%, $secondary 100%);
  padding: 100px 0;
}
```

### Responsive Design Patterns

#### Mobile-First Approach

```html
<!-- Stack on mobile, side-by-side on desktop -->
<div class="container">
    <div class="row">
        <div class="col-12 col-lg-8">
            <h1>Main Content</h1>
            <p>This takes full width on mobile, 8 columns on large screens.</p>
        </div>
        <div class="col-12 col-lg-4">
            <h2>Sidebar</h2>
            <p>This takes full width on mobile, 4 columns on large screens.</p>
        </div>
    </div>
</div>
```

#### Hiding/Showing Elements

```html
<!-- Show only on mobile -->
<div class="d-block d-md-none">
    <button class="btn btn-primary w-100">Mobile Menu</button>
</div>

<!-- Hide on mobile, show on tablet+ -->
<div class="d-none d-md-block">
    <nav>Desktop Navigation</nav>
</div>

<!-- Different layouts for different screens -->
<div class="row">
    <div class="col-12 col-md-6 col-lg-3">
        1 column on mobile, 2 on tablet, 4 on desktop
    </div>
    <!-- Repeat 3 more times -->
</div>
```

### JavaScript Components Programming

#### Modal Control with JavaScript

```javascript
// Show modal programmatically
var myModal = new bootstrap.Modal(document.getElementById('myModal'));
myModal.show();

// Hide modal
myModal.hide();

// Listen for events
document.getElementById('myModal').addEventListener('shown.bs.modal', function () {
    console.log('Modal is now visible');
});

document.getElementById('myModal').addEventListener('hidden.bs.modal', function () {
    console.log('Modal is now hidden');
});
```

#### Toast Notifications

```javascript
function showToast(message, type = 'info') {
    const toastHTML = `
        <div class="toast align-items-center text-white bg-${type} border-0" role="alert">
            <div class="d-flex">
                <div class="toast-body">${message}</div>
                <button type="button" class="btn-close btn-close-white me-2 m-auto" data-bs-dismiss="toast"></button>
            </div>
        </div>
    `;
    
    const container = document.querySelector('.toast-container');
    container.insertAdjacentHTML('beforeend', toastHTML);
    
    const toastElement = container.lastElementChild;
    const toast = new bootstrap.Toast(toastElement);
    toast.show();
    
    // Remove from DOM after hidden
    toastElement.addEventListener('hidden.bs.toast', function() {
        toastElement.remove();
    });
}

// Usage
showToast('Operation successful!', 'success');
showToast('An error occurred!', 'danger');
```

#### Dynamic Tab Content

```javascript
// Switch tabs programmatically
var triggerEl = document.querySelector('#myTab button[data-bs-target="#profile"]');
var tab = new bootstrap.Tab(triggerEl);
tab.show();

// Listen for tab changes
var tabEl = document.querySelectorAll('button[data-bs-toggle="tab"]');
tabEl.forEach(function(tab) {
    tab.addEventListener('shown.bs.tab', function(event) {
        console.log('Active tab:', event.target);
        console.log('Previous tab:', event.relatedTarget);
    });
});
```

#### Collapse with Custom Triggers

```javascript
// Toggle collapse programmatically
var collapseElement = document.getElementById('myCollapse');
var collapse = new bootstrap.Collapse(collapseElement, {
    toggle: false
});

// Methods
collapse.show();
collapse.hide();
collapse.toggle();

// Events
collapseElement.addEventListener('show.bs.collapse', function() {
    console.log('Collapse is opening');
});

collapseElement.addEventListener('shown.bs.collapse', function() {
    console.log('Collapse is now visible');
});
```

### Performance Optimization

#### Load Only What You Need

```html
<!-- Instead of full Bootstrap -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- Use Bootstrap's source and import only specific modules -->
<!-- Requires Sass compilation -->
```

```scss
// custom.scss
// Import only what you need
@import "bootstrap/scss/functions";
@import "bootstrap/scss/variables";
@import "bootstrap/scss/mixins";
@import "bootstrap/scss/root";
@import "bootstrap/scss/reboot";
@import "bootstrap/scss/grid";
@import "bootstrap/scss/utilities";
@import "bootstrap/scss/buttons";
@import "bootstrap/scss/navbar";
// ... only the components you use
```

#### Lazy Loading Images

```html
<img src="placeholder.jpg" 
     data-src="actual-image.jpg" 
     class="img-fluid lazy" 
     alt="Description">

<script>
document.addEventListener("DOMContentLoaded", function() {
    var lazyImages = [].slice.call(document.querySelectorAll("img.lazy"));
    
    if ("IntersectionObserver" in window) {
        let lazyImageObserver = new IntersectionObserver(function(entries) {
            entries.forEach(function(entry) {
                if (entry.isIntersecting) {
                    let lazyImage = entry.target;
                    lazyImage.src = lazyImage.dataset.src;
                    lazyImage.classList.remove("lazy");
                    lazyImageObserver.unobserve(lazyImage);
                }
            });
        });
        
        lazyImages.forEach(function(lazyImage) {
            lazyImageObserver.observe(lazyImage);
        });
    }
});
</script>
```

### Bootstrap with Popular Frameworks

#### Bootstrap + React

```jsx
// Install react-bootstrap
// npm install react-bootstrap bootstrap

import { Container, Row, Col, Button, Card } from 'react-bootstrap';
import 'bootstrap/dist/css/bootstrap.min.css';

function App() {
    return (
        <Container>
            <Row>
                <Col md={6}>
                    <Card>
                        <Card.Body>
                            <Card.Title>Card Title</Card.Title>
                            <Card.Text>Card content</Card.Text>
                            <Button variant="primary">Click Me</Button>
                        </Card.Body>
                    </Card>
                </Col>
            </Row>
        </Container>
    );
}
```

#### Bootstrap + Vue

```vue
<!-- Install bootstrap-vue-3 -->
<!-- npm install bootstrap bootstrap-vue-3 -->

<template>
  <b-container>
    <b-row>
      <b-col md="6">
        <b-card title="Card Title">
          <b-card-text>Card content</b-card-text>
          <b-button variant="primary">Click Me</b-button>
        </b-card>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import { BContainer, BRow, BCol, BCard, BButton } from 'bootstrap-vue-3';

export default {
  components: {
    BContainer,
    BRow,
    BCol,
    BCard,
    BButton
  }
}
</script>
```

---

## Real-World Projects

### Project 1: Landing Page

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Modern Landing Page</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        .hero {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 100px 0;
        }
        .feature-icon {
            font-size: 3rem;
            margin-bottom: 1rem;
        }
    </style>
</head>
<body>
    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container">
            <a class="navbar-brand" href="#">MyBrand</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ms-auto">
                    <li class="nav-item"><a class="nav-link" href="#features">Features</a></li>
                    <li class="nav-item"><a class="nav-link" href="#pricing">Pricing</a></li>
                    <li class="nav-item"><a class="nav-link" href="#contact">Contact</a></li>
                </ul>
            </div>
        </div>
    </nav>

    <!-- Hero Section -->
    <section class="hero text-center">
        <div class="container">
            <h1 class="display-3 fw-bold mb-4">Build Amazing Things</h1>
            <p class="lead mb-4">Create stunning websites with our modern platform</p>
            <button class="btn btn-light btn-lg me-2">Get Started</button>
            <button class="btn btn-outline-light btn-lg">Learn More</button>
        </div>
    </section>

    <!-- Features Section -->
    <section id="features" class="py-5">
        <div class="container">
            <h2 class="text-center mb-5">Our Features</h2>
            <div class="row g-4">
                <div class="col-md-4">
                    <div class="text-center">
                        <div class="feature-icon">🚀</div>
                        <h3>Fast Performance</h3>
                        <p>Lightning-fast load times and optimized performance</p>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="text-center">
                        <div class="feature-icon">🎨</div>
                        <h3>Beautiful Design</h3>
                        <p>Modern, responsive designs that look great everywhere</p>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="text-center">
                        <div class="feature-icon">🔒</div>
                        <h3>Secure</h3>
                        <p>Enterprise-grade security to keep your data safe</p>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Pricing Section -->
    <section id="pricing" class="py-5 bg-light">
        <div class="container">
            <h2 class="text-center mb-5">Pricing Plans</h2>
            <div class="row g-4">
                <div class="col-md-4">
                    <div class="card">
                        <div class="card-body text-center">
                            <h3 class="card-title">Basic</h3>
                            <h2 class="my-4">$9<small>/mo</small></h2>
                            <ul class="list-unstyled">
                                <li class="mb-2">✓ 10 Projects</li>
                                <li class="mb-2">✓ 5GB Storage</li>
                                <li class="mb-2">✓ Email Support</li>
                            </ul>
                            <button class="btn btn-outline-primary">Choose Plan</button>
                        </div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="card border-primary">
                        <div class="card-body text-center">
                            <span class="badge bg-primary mb-2">Popular</span>
                            <h3 class="card-title">Pro</h3>
                            <h2 class="my-4">$29<small>/mo</small></h2>
                            <ul class="list-unstyled">
                                <li class="mb-2">✓ Unlimited Projects</li>
                                <li class="mb-2">✓ 50GB Storage</li>
                                <li class="mb-2">✓ Priority Support</li>
                            </ul>
                            <button class="btn btn-primary">Choose Plan</button>
                        </div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="card">
                        <div class="card-body text-center">
                            <h3 class="card-title">Enterprise</h3>
                            <h2 class="my-4">$99<small>/mo</small></h2>
                            <ul class="list-unstyled">
                                <li class="mb-2">✓ Unlimited Everything</li>
                                <li class="mb-2">✓ 500GB Storage</li>
                                <li class="mb-2">✓ 24/7 Phone Support</li>
                            </ul>
                            <button class="btn btn-outline-primary">Choose Plan</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Contact Section -->
    <section id="contact" class="py-5">
        <div class="container">
            <h2 class="text-center mb-5">Get In Touch</h2>
            <div class="row justify-content-center">
                <div class="col-md-6">
                    <form>
                        <div class="mb-3">
                            <label for="name" class="form-label">Name</label>
                            <input type="text" class="form-control" id="name" required>
                        </div>
                        <div class="mb-3">
                            <label for="email" class="form-label">Email</label>
                            <input type="email" class="form-control" id="email" required>
                        </div>
                        <div class="mb-3">
                            <label for="message" class="form-label">Message</label>
                            <textarea class="form-control" id="message" rows="4" required></textarea>
                        </div>
                        <button type="submit" class="btn btn-primary w-100">Send Message</button>
                    </form>
                </div>
            </div>
        </div>
    </section>

    <!-- Footer -->
    <footer class="bg-dark text-white py-4">
        <div class="container text-center">
            <p class="mb-0">&copy; 2024 MyBrand. All rights reserved.</p>
        </div>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

### Project 2: Dashboard

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Admin Dashboard</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            font-size: .875rem;
        }
        .sidebar {
            position: fixed;
            top: 0;
            bottom: 0;
            left: 0;
            z-index: 100;
            padding: 48px 0 0;
            box-shadow: inset -1px 0 0 rgba(0, 0, 0, .1);
        }
        .sidebar-sticky {
            position: relative;
            top: 0;
            height: calc(100vh - 48px);
            padding-top: .5rem;
            overflow-x: hidden;
            overflow-y: auto;
        }
        main {
            margin-left: 240px;
        }
        .navbar {
            box-shadow: 0 2px 4px rgba(0,0,0,.1);
        }
    </style>
</head>
<body>
    <!-- Navbar -->
    <nav class="navbar navbar-dark bg-dark fixed-top">
        <div class="container-fluid">
            <a class="navbar-brand" href="#">Dashboard</a>
            <div class="d-flex">
                <div class="dropdown">
                    <button class="btn btn-dark dropdown-toggle" type="button" data-bs-toggle="dropdown">
                        Admin User
                    </button>
                    <ul class="dropdown-menu dropdown-menu-end">
                        <li><a class="dropdown-item" href="#">Profile</a></li>
                        <li><a class="dropdown-item" href="#">Settings</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Logout</a></li>
                    </ul>
                </div>
            </div>
        </div>
    </nav>

    <!-- Sidebar -->
    <nav class="col-md-3 col-lg-2 d-md-block bg-light sidebar">
        <div class="sidebar-sticky pt-3">
            <ul class="nav flex-column">
                <li class="nav-item">
                    <a class="nav-link active" href="#">📊 Dashboard</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">📦 Orders</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">🛍️ Products</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">👥 Customers</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">📈 Analytics</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">⚙️ Settings</a>
                </li>
            </ul>
        </div>
    </nav>

    <!-- Main Content -->
    <main class="col-md-9 ms-sm-auto col-lg-10 px-md-4">
        <div class="pt-3 pb-2 mb-3 border-bottom">
            <h1 class="h2">Dashboard</h1>
        </div>

        <!-- Stats Cards -->
        <div class="row g-3 mb-4">
            <div class="col-md-3">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title text-muted">Revenue</h5>
                        <h2 class="mb-0">$45,231</h2>
                        <small class="text-success">+12.5% from last month</small>
                    </div>
                </div>
            </div>
            <div class="col-md-3">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title text-muted">Orders</h5>
                        <h2 class="mb-0">1,234</h2>
                        <small class="text-success">+8.2% from last month</small>
                    </div>
                </div>
            </div>
            <div class="col-md-3">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title text-muted">Customers</h5>
                        <h2 class="mb-0">856</h2>
                        <small class="text-danger">-2.1% from last month</small>
                    </div>
                </div>
            </div>
            <div class="col-md-3">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title text-muted">Conversion</h5>
                        <h2 class="mb-0">3.2%</h2>
                        <small class="text-success">+0.5% from last month</small>
                    </div>
                </div>
            </div>
        </div>

        <!-- Recent Orders Table -->
        <div class="card">
            <div class="card-header">
                <h5 class="mb-0">Recent Orders</h5>
            </div>
            <div class="card-body">
                <div class="table-responsive">
                    <table class="table table-hover">
                        <thead>
                            <tr>
                                <th>Order ID</th>
                                <th>Customer</th>
                                <th>Product</th>
                                <th>Amount</th>
                                <th>Status</th>
                                <th>Date</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>#12345</td>
                                <td>John Doe</td>
                                <td>Product A</td>
                                <td>$299.00</td>
                                <td><span class="badge bg-success">Completed</span></td>
                                <td>2024-01-15</td>
                            </tr>
                            <tr>
                                <td>#12346</td>
                                <td>Jane Smith</td>
                                <td>Product B</td>
                                <td>$499.00</td>
                                <td><span class="badge bg-warning">Pending</span></td>
                                <td>2024-01-15</td>
                            </tr>
                            <tr>
                                <td>#12347</td>
                                <td>Bob Johnson</td>
                                <td>Product C</td>
                                <td>$149.00</td>
                                <td><span class="badge bg-info">Processing</span></td>
                                <td>2024-01-14</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </main>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

---

## Best Practices

### 1. Mobile-First Development
Always design for mobile first, then enhance for larger screens.

### 2. Use Semantic HTML
```html
<!-- Good -->
<nav class="navbar">...</nav>
<main>...</main>
<footer>...</footer>

<!-- Avoid -->
<div class="navbar">...</div>
<div>...</div>
<div>...</div>
```

### 3. Accessibility
```html
<!-- Always include alt text -->
<img src="image.jpg" alt="Description of image">

<!-- Use ARIA labels -->
<button class="navbar-toggler" aria-label="Toggle navigation">

<!-- Proper heading hierarchy -->
<h1>Main Title</h1>
<h2>Section Title</h2>
<h3>Subsection Title</h3>
```

### 4. Performance
- Minimize custom CSS by using Bootstrap utilities
- Load JavaScript at the end of the body
- Use responsive images with srcset
- Lazy load images below the fold

### 5. Consistent Spacing
Use Bootstrap's spacing utilities consistently:
```html
<div class="mt-4 mb-3">Content</div>
<div class="p-3">Content</div>
```

### 6. Don't Fight the Framework
Work with Bootstrap's conventions rather than against them.

### 7. Keep It Simple
Use Bootstrap components as they are before customizing.

---

## Common Patterns & Solutions

### Centering Content Vertically and Horizontally
```html
<div class="d-flex justify-content-center align-items-center vh-100">
    <div>Centered Content</div>
</div>
```

### Card Grid Layout
```html
<div class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4">
    <div class="col">
        <div class="card h-100">
            <img src="..." class="card-img-top" alt="...">
            <div class="card-body">
                <h5 class="card-title">Card title</h5>
                <p class="card-text">Content</p>
            </div>
        </div>
    </div>
    <!-- Repeat for more cards -->
</div>
```

### Sticky Footer
```html
<body class="d-flex flex-column min-vh-100">
    <nav>...</nav>
    <main class="flex-grow-1">...</main>
    <footer class="mt-auto">...</footer>
</body>
```

### Image Overlay with Text
```html
<div class="position-relative">
    <img src="image.jpg" class="img-fluid" alt="...">
    <div class="position-absolute top-50 start-50 translate-middle text-white">
        <h1>Overlay Text</h1>
    </div>
</div>
```

---

## Resources for Continued Learning

### Official Resources
- **Bootstrap Documentation**: [getbootstrap.com](https://getbootstrap.com)
- **Bootstrap Icons**: [icons.getbootstrap.com](https://icons.getbootstrap.com)
- **Bootstrap Blog**: Official blog with tips and updates

### Community Resources
- **Bootstrap Themes**: [themes.getbootstrap.com](https://themes.getbootstrap.com)
- **Start Bootstrap**: Free templates and themes
- **Bootsnipp**: Code snippets and examples
- **CodePen**: Search for Bootstrap examples

### Tools
- **Bootstrap Studio**: Visual design tool
- **Pingendo**: Bootstrap builder
- **LayoutIt!**: Grid builder

---

## Conclusion

You've now learned Bootstrap from the fundamentals to advanced techniques! Practice by building real projects, and remember:

1. **Start simple** - Use default Bootstrap before customizing
2. **Mobile-first** - Always design for mobile, then scale up
3. **Semantic HTML** - Write clean, accessible markup
4. **Use utilities** - Leverage Bootstrap's utility classes
5. **Keep learning** - Bootstrap evolves, stay updated

The key to mastery is building real projects. Start with simple pages and gradually increase complexity. Good luck with your Bootstrap journey!