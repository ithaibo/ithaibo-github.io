1. 默认ViewStub在创建时会设置为gone;
``` java
      public ViewStub(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context);
        // ...
        setVisibility(GONE);
        // ...
    }
```

2. 如果手动对visibility设置为visible, invisible时，会执行inflate
``` java
    public void setVisibility(int visibility) {
        // ...
            if (visibility == VISIBLE || visibility == INVISIBLE) {
                inflate();
        // ...
    }
```

3. ViewStub在执行完inflate后，会将该ViewStub对象置为null

4. ViewStub 在inflate后会将stub的LayoutParams设置给inflatedView
``` java
    public View inflate() {
        // ...
        replaceSelfWithView(view, parent);
        // ...
    }

    private void replaceSelfWithView(View view, ViewGroup parent) {
        // ...
        final ViewGroup.LayoutParams layoutParams = getLayoutParams();
        if (layoutParams != null) {
            parent.addView(view, index, layoutParams);
        } else {
            parent.addView(view, index);
        }
    }
```

5. ViewStub为final，不能继承