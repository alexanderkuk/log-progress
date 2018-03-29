## Widget based progress bar for Jupyter (IPython Notebook)

Requires [ipywidgets](https://ipywidgets.readthedocs.io) to be installed ([installation instructions](https://ipywidgets.readthedocs.io/en/stable/user_install.html#installation)).

### Code
Just copy and paste it into your project:
```python
def log_progress(sequence, every=None, size=None, name='Items'):
    from ipywidgets import IntProgress, HTML, VBox
    from IPython.display import display

    is_iterator = False
    if size is None:
        try:
            size = len(sequence)
        except TypeError:
            is_iterator = True
    if size is not None:
        if every is None:
            if size <= 200:
                every = 1
            else:
                every = int(size / 200)     # every 0.5%
    else:
        assert every is not None, 'sequence is iterator, set every'

    if is_iterator:
        progress = IntProgress(min=0, max=1, value=1)
        progress.bar_style = 'info'
    else:
        progress = IntProgress(min=0, max=size, value=0)
    label = HTML()
    box = VBox(children=[label, progress])
    display(box)

    index = 0
    try:
        for index, record in enumerate(sequence, 1):
            if index == 1 or index % every == 0:
                if is_iterator:
                    label.value = '{name}: {index} / ?'.format(
                        name=name,
                        index=index
                    )
                else:
                    progress.value = index
                    label.value = u'{name}: {index} / {size}'.format(
                        name=name,
                        index=index,
                        size=size
                    )
            yield record
    except:
        progress.bar_style = 'danger'
        raise
    else:
        progress.bar_style = 'success'
        progress.value = index
        label.value = "{name}: {index}".format(
            name=name,
            index=str(index or '?')
        )
```

Use the progress bar like this:

```python
things = range(100)
for thing in log_progress(things, every=1):
    do_it(thing)
```

### Examples
Progress bar changes its color based on outcome:

![](https://habrastorage.org/files/d7a/1f5/9f6/d7a1f59f61634d63a42b274ba186d1ba.gif)

![](https://habrastorage.org/files/1bc/544/e8a/1bc544e8a50b419382d0fc090e087cce.gif)

Iterators are supported:

![](https://habrastorage.org/files/712/255/d77/712255d77fd5473b8113e7bfc1bd852f.gif)

More then one progress bar can be in a sigle cell:

![](https://habrastorage.org/files/1b4/48f/9a5/1b448f9a5b74456091eb5b16799c7c3e.gif)

![](https://habrastorage.org/files/95d/c00/1df/95dc001dffb24852999a73ae8129a209.gif)

They can even be from different threads:

![](https://habrastorage.org/files/e64/69a/fe5/e6469afe59ed485c84565a672d24cd50.gif)

