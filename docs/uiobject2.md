# UiObject2 API Usage

This doc shows how Snippet UiAutomator implements the
[UiObject2](https://developer.android.com/reference/androidx/test/uiautomator/UiObject2)
to represent an UI object.

## Basic

*   Search criteria can be saved as `UiObject2`.
*   UI object lazily resolves references to actual UI elements only when taking
    an action on `UiObject2`:

> Important: It is not saved a real Android UiObject2 as a Python object, it is
> just a lazy evaluation to avoid to declare search criteria repeatedly.

```python
example = ad.ui(text='Example')
while not example.exists:
  time.sleep(1)
```

## Information

```python
>>> example = ad.ui(text='Example')

# Returns a list of child objects which is below the target object.
>>> example.children
[...]

>>> example.class_name
'android.widget.TextView'

>>> example.description
'Example'

>>> example.hint
'Example'

>>> example.package_name
'com.android.example'

>>> example.resource_id
'android:id/title'

>>> example.text
'Example'

>>> example.checkable
False

>>> example.checked
False

>>> example.clickable
False

>>> example.enabled
True

>>> example.focusable
False

>>> example.focused
False

>>> example.long_clickable
False

>>> example.scrollable
False

>>> example.selected
False

>>> example.display_id
0

>>> example.visible_bounds.left
198

>>> example.visible_bounds.top
1343

>>> example.visible_bounds.right
340

>>> example.visible_bounds.bottom
1402

>>> example.visible_center.x
269

>>> example.visible_center.y
1372

# Gets all information one time.
>>> example.info
{'childCount': 0,
 'displayId': 0,
 'className': 'android.widget.TextView',
 'contentDescription': 'Example',
 'hint': 'Example',
 'packageName': 'com.android.example',
 'text': 'Example',
 'checkable': False,
 'checked': False,
 'clickable': False,
 'enabled': True,
 'focusable': False,
 'focused': False,
 'longClickable': False,
 'scrollable': False,
 'selected': False,
 'visibleBounds': {'left': 198, 'top': 1343, 'right': 340, 'bottom': 1402},
 'visibleCenter': {'x': 269, 'y': 1372}}
```

## Operation

### Count Matched Objects

Count the number of objects that match the search criteria

```python
>>> ad.ui(text='Example').count
0
```

### Check Exists

Check if an object exists

```python
>>> ad.ui(text='Example').exists
False
```

Raise with a custom error message if the object doesn't exist.

```python
>>> ad.ui(text='Example').assert_exists('Fail to find my example')
Traceback (most recent call last):
  File "/snippet_uiautomator/uiobject2.py", line 739, in assert_exists
    _ = self.exists
        ^^^^^^^^^^^
  File "/snippet_uiautomator/uiobject2.py", line 729, in exists
    raise errors.UiObjectSearchError(
snippet_uiautomator.errors.UiObjectSearchError: <AndroidDevice|GOOG1234567890> Not found Selector{'text': 'Example'}

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/snippet_uiautomator/uiobject2.py", line 741, in assert_exists
    raise errors.UiObjectSearchError(error_msg, self._device) from e
snippet_uiautomator.errors.UiObjectSearchError: <AndroidDevice|GOOG1234567890> Fail to find my example
```

### Clear/Set Text

Clear/Set the text content on
[EditText](https://developer.android.com/reference/android/widget/EditText)

```python
>>> ad.ui(text='Example').clear_text()
True

>>> ad.ui(text='Example').set_text('Hello World')
True
```

### Click

#### Short click

```python
>>> ad.ui(text='Example').click()
True

# Clicks then releases after 10 seconds.
>>> click_then_hold_time = datetime.timedelta(seconds=10)
>>> ad.ui(text='Example').click(click_then_hold_time)
True

# Clicks on the bottom-right corner.
>>> ad.ui(text="Settings").click.bottomright()

# Clicks on the top-left corner.
>>> ad.ui(text="Settings").click.topleft()

# Clicks and waits for a new window.
>>> wait_time_after_click = datetime.timedelta(seconds=10)
>>> ad.ui(text="Settings").click.wait(wait_time_after_click)
```

#### Long click

```python
>>> ad.ui(text='Example').long_click()
True
```

#### Click on point

Clicks on a point within this object's visible bounds, see
[UiObject2#click(android.graphics.Point,long)](https://developer.android.com/reference/androidx/test/uiautomator/UiObject2#click\(android.graphics.Point,long\))
for more details.

```python
>>> example = ad.ui(text="Settings")
>>> example_center = example.visible_center

# Clicks on the center point.
>>> example.click(x=example_center.x, y=example_center.y)

# Clicks then releases after 10 seconds on the center point.
>>> click_then_hold_time = datetime.timedelta(seconds=10)
>>> example.click(x=example_center.x, y=example_center.y, duration=click_then_hold_time)
```

### Drag

Drag to specific point

```python
>>> ad.ui(text='Example').drag.to(x=100, y=500)
True

# Defines the speed that perform this gesture in pixels per second.
>>> ad.ui(text='Example').drag.to(x=100, y=500, speed=50)
True
```

Drag to another object

```python
>>> ad.ui(text='Example').drag.to(text='Another Example')
True

# Defines the speed that perform this gesture in pixels per second.
>>> ad.ui(text='Example').drag.to(text='Another Example', speed=50)
True
```

### Fling/Swipe

For the difference between `fling` and `swipe`, please refer to
[Material Design](https://m1.material.io/patterns/gestures.html#gestures-drag-swipe-or-fling-details).

These methods support:

-   Setting margin of the action, details please refer to
    [UiObject2#setGestureMargin](https://developer.android.com/reference/kotlin/androidx/test/uiautomator/UiObject2#setGestureMargin\(int\))

    -   `margin` in pixels

    -   `percent` in percentage of the object's size

-   4 directions

    -   `down`

    -   `left`

    -   `right`

    -   `up`

```python
# Defines the speed that perform this gesture in pixels per second.
>>> ad.ui(text='Example').fling.down(speed=100)
True

# Sets the percentage of margins before fling.
>>> ad.ui(text='Example').fling(percent=0.1).down()
True

# Defines the length of the swipe as a percentage of this object's size.
>>> ad.ui(text='Example').swipe.right(percent=50, speed=100)
True

# Sets the margins in pixels before swipe.
>>> ad.ui(text='Example').swipe(margin=10).down()
True

# Sets both the margins in pixels and speed.
>>> ad.ui(text='Example').swipe(margin=10).down(speed=100)
True
```

> Important: Fling action does not support `percent` as direction parameters,
> please refer to
> [UiObject2#fling](https://developer.android.com/reference/androidx/test/uiautomator/UiObject2#fling).

### Scroll

This method supports 4 directions:

-   down
-   left
-   right
-   up

#### Scroll with Specific Distance

```python
# Scrolls down a distance of 50% of the visible size of this object.
>>> ad.ui(text='Example').scroll.down(percent=50)
True

# Scrolls right a distance of 50% of the visible size of this object in 100 pixels per second.
>>> ad.ui(text='Example').swipe.right(percent=50, speed=100)
True
```

#### Scroll to End

Scroll until reach the end of that direction.

```python
# Scrolls up until reach the end.
>>> ad.ui(scrollable=True).scroll.up()
True

# Scrolls left until reach the end.
>>> ad.ui(scrollable=True).scroll.left()
True
```

#### Scroll to Specific Object

```python
# Scrolls down until find the target object or reach the end.
>>> ad.ui(scrollable=True).scroll.down(clazz='android.widget.TextView', text='Location')
True

# Scrolls right until find the target object or reach the end.
>>> ad.ui(scrollable=True).scroll.right(clazz='android.widget.TextView', text='Location')
True

# Scrolls up until find the target object or reach the end.
target = ad.ui(text='Example').right(res='android:id/switch_widget')
>>> ad.ui(scrollable=True).scroll.right(target=target)
True
```

#### Scroll to Specific Object, Then Click

```python
# Scrolls up until find the target object or reach the end. If found, clicks it.
>>> ad.ui(scrollable=True).scroll.up.click(clazz='android.widget.TextView', text='Location')
True
```

### Pinch

```python
# Defines the size of the pinch as a percentage of this object's size.
>>> ad.ui(text='Example').pinch.close(percent=100)
True

# Defines the speed at which to perform this gesture in pixels per second.
>>> ad.ui(text='Example').pinch.open(percent=100, speed=100)
True
```

#### Scroll with Specific Margin

If the scroll gesture starts too close to the edge and the scrolling action
doesn't perform correctly, try setting a margin.

```python
# Sets the margins in pixels before scrolling.
>>> ad.ui(text='Example').scroll(margin=10).down()
True

# Sets the percentage of margins before scrolling.
>>> ad.ui(text='Example').scroll(percent=0.1).down()
True
```

### Wait

#### Until Appear

Allow a set time for the object to become available.

```python
>>> _WAIT_TIME = datetime.timedelta(seconds=3)
>>> ad.ui(text='Example').wait.exists(_WAIT_TIME)
True
```

Raise with a custom error message if the object doesn't show before timeout.

```python
>>> ad.ui(text='Example').wait.assert_exists('Fail to find my example', _WAIT_TIME)
Traceback (most recent call last):
  File "/snippet_uiautomator/uiobject2.py", line 579, in assert_exists
    self.exists(timeout, raise_error=True)
  File "/snippet_uiautomator/uiobject2.py", line 566, in exists
    raise errors.UiObjectSearchError(
snippet_uiautomator.errors.UiObjectSearchError: <AndroidDevice|GOOG1234567890> Not found Selector{'text': 'Example'} over 3000 ms

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/snippet_uiautomator/uiobject2.py", line 581, in assert_exists
    raise errors.UiObjectSearchError(error_msg, self._device) from e
snippet_uiautomator.errors.UiObjectSearchError: <AndroidDevice|GOOG1234567890> Fail to find my example
```

#### Until Disappear

Allow a set time for the object to become unavailable.

```python
>>> ad.ui(text='Example').wait.gone(_WAIT_TIME)
True
```

Raise with a custom error message if the object doesn't vanish before timeout.

```python
>>> ad.ui(text='Example').wait.assert_gone('Fail to exit my example', _WAIT_TIME)
Traceback (most recent call last):
  File "/snippet_uiautomator/uiobject2.py", line 624, in assert_gone
    self.gone(timeout, raise_error=True)
  File "/snippet_uiautomator/uiobject2.py", line 610, in gone
    raise errors.UiObjectSearchError(
snippet_uiautomator.errors.UiObjectSearchError: <AndroidDevice|GOOG1234567890> Still found Selector{'text': '48181FDAP0032U'} over 3000 ms

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/snippet_uiautomator/uiobject2.py", line 626, in assert_gone
    raise errors.UiObjectSearchError(error_msg, self._device) from e
snippet_uiautomator.errors.UiObjectSearchError: <AndroidDevice|GOOG1234567890> Fail to exit my example
```

#### Click Until Appear

```python
>>> ad.ui(text='Example').wait.click(_WAIT_TIME)
True
```

### Search

Search all child objects under this object.

```python
# Returns a list of matched objects which is below the target object.
>>> ad.ui(text='Example').find(text='Child Example')
[...]

# Returns True if there is a matched object below the target object.
>>> ad.ui(text='Example').has(text='Child Example')
True
```
