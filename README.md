[![Build Status](https://travis-ci.org/testingrequired/vom.svg?branch=master)](https://travis-ci.org/testingrequired/vom)
[![PyPI version](https://badge.fury.io/py/vom.svg)](https://badge.fury.io/py/vom)

# vom

`vom` (View Object Modal) is an opinionated framework for writing page objects for selenium tests/scripts

## View Object vs Page Object

The term "page" is outdated in the context of modern web applications. The term "view" more aligned with the usage of self contained components (e.g. React, Angular, Vue). They represent the same concept however.

## Installation

```bash
$ pip install vom
```

## Getting Started

```python
from vom import View

class Login(View):
    @property
    def username(self):
        return self.find_element_by_name("username")
    
    @property
    def password(self):
        return self.find_element_by_name("password")
    
    @property
    def login_button(self):
        return self.find_element_by_id("loginBtn")
```

Then using:

```python
login = Login(lambda: driver.find_element_by_id("loginForm"))
```

or

```python
login = Login(driver)
login.root = lambda: driver.find_element_by_id("loginForm")
```

## Element Properties

Elements should be properties on the `View` to ensure they never throw a `StaleElementReference`. These element properties are a `View` themselves all the way down.

## Root Element

The `root` element of the `View` is the underlying `WebElement`.

### Setting

The `root` can be set by assigning a `Callable[[], WebElement]`.

```python
login.root = lambda: driver.find_element_by_id("loginForm")
```

## Parent View

The `parent` is a reference to its parent `View`. Element properties `parent` will be the `View` that defined them.

## API

`View` mirrors most of the `WebElement` API with additional utility methods:

### Finding Elements

The `find_element`/`find_elements` family of methods all return `View` instead of `WebElement` scoped within the `root` `WebElement`.

#### Custom View Class

Element/s are wrapped in a `View` class before being returned from `find_element`/`find_elements` family of methods. A custom subclass of `View` can be passed using the `view_cls` argument.

```python
class CustomView(View): pass

view.find_element(By.CSS_SELECTOR, "input[placeholder='username']", view_cls=CustomView)
view.find_element_by_css_selector("p", view_cls=CustomView)
```

This is useful if you have common logic across multiple elements.

#### By text

Similar to `find_element_by_link_text` and etc but works for all tag names within the `View`.

- `find_elements_by_text(value, selector="*")`
- `find_element_by_text(value, selector="*")`
- `find_elements_by_partial_text(value, selector="*")`
- `find_element_by_partial_text(value, selector="*")`

##### Custom Selector

The default css selector used is `*` but any valid css selector can be used to filter elements against.

### Properties

- `title` Returns the `root` element's `title`

### State

- `has_class(value)` Returns if the `root` element of the `View` has a class

### Content

- `inner_html` Returns the `root` element's `innerHTML`
- `outer_html` Returns the `root` element's `outerHTML`
- `inner_text` Returns the `root` element's `innerText`

### Waiting

- `wait_until_displayed(timeout=10)`
- `wait_until_not_displayed(timeout=10)`

### Actions

- `focus()`
- `blur()`
- `send_keys(value, clear=False)` Set `clear` to true to clear the input before `send_keys`

### Execute Script

Similar to `driver.execute_script` but `arguments[0]` is a reference to the `root` element of the `View`.

- `execute_script(script, *args)`
- `execute_async_script(script, *args)`

### Transform

- `as_select` Return the `root` element wrapped in a `Select`