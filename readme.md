# window-layout management library

Split a frame or window into some windows according to a layout recipe.

## Example

```el
;; Layout function
; -> three pane layout.
(setq wm ; <-- window management object
      (wlf:layout 
       '(| (:left-size-ratio 0.3) 
           folder 
           (- (:upper-max-size 15) 
              summary 
              message))
       '((:name folder 
          :buffer "folder buffer")
         (:name summary
          :buffer "summary buffer")
         (:name message
          :buffer "message buffer")
        )))

;; Window controlling
(wlf:show    wm 'summary)
(wlf:hide    wm 'summary)
(wlf:toggle  wm 'summary)
(wlf:select  wm 'summary)
(wlf:toggle-maximize  wm 'summary)

;; Window updating
(wlf:refresh wm)
(wlf:reset-window-sizes wm)
(wlf:reset-init wm)

;; Accessing a buffer
(wlf:get-buffer wm 'summary) ; -> <#buffer object or buffer name>
(wlf:set-buffer wm 'summary "*scratch*")

;; Accessing a window
(wlf:get-window wm 'summary) ; -> <#window object>

;; Layout hook
(defun wlf:test-hook (wset) (message "HOOK : %s" wset))
(wlf:layout-hook-add wm 'wlf:test-hook)
(wlf:layout-hook-remove wm 'wlf:test-hook)
```

## API Document

### Layout function

- `wlf:layout`
    - Lay out windows and return a management object.
    - Arguments: (see the following sections)
        - recipe : layout recipe list
        - window-params : window options
        - subwindow-p : optional flag
    - Return
        - a wset object, the window management object
- `wlf:no-layout`
    - Just return a management object, does not change window layout.
    - The arguments and return value are the same as `wlf:layout` ones.

#### Layout recipe:

```
( (split type) (split option) 
               (left window name or recipe)
               (right window name or recipe) )
```

- **split type**
    - `-` : split vertically
    - `|` : split horizontally
- **split option** (the prefix 'left' can be replaced by 'right', 'upper' and 'lower'.)
    - `:left-size`  (column or row number) window size
    - `:left-max-size`  (column or row number) if window size is larger than this value, the window is shrunken.
    - `:left-size-ratio`  (0.0 - 1.0) window size ratio. the size of the other side is the rest.

Note: 
- The split option can be omitted.
- The size parameters, `:size`, `:max-size` and `:size-ratio`, are mutually exclusive.
- The size of a window is related with one of the other side window. So, if both side windows set conflict size parameters, the window size may not be adjusted as you write.

#### Window options:

- `:name`  the window name.
- `:buffer` a buffer name or a buffer object to show the window. If nil or omitted, the current buffer remains.
- `:default-hide`  (t/nil) if t, the window is hided initially. (default: nil)
- `:fix-size`  (t/nil) if t, when the windows are laid out again, the window size is remained. (default: nil)

#### subwindow-p option:

If this option is not nil, this function splits the windows within
the current window. If this option is nil or omitted, this function
uses the entire space of the current frame. Because some user
actions and complicated window layouts may cause unexpected split
behaviors, it is easy to use the entire space of a frame.

#### Return value (Window management object):

You should not access the management object directly, because it is not 
intended direct access.
You can make some management objects to switch the window layout.

### Access functions

- `wlf:get-buffer`
- `wlf:set-buffer`
- `wlf:wset-live-p`

### Control functions

- `wlf:refresh`
- `wlf:reset-window-sizes`
- `wlf:reset-init`
- `wlf:show`
- `wlf:hide`
- `wlf:toggle`
- `wlf:toggle-maximize`
- `wlf:select`

### Layout hook

After splitting windows, registered hook are called with one
argument, the window management object.

- `wlf:layout-hook-add`
- `wlf:layout-hook-remove`

## License

GPLv3
