# neuroviz

A set of notebooks to introduce neuroscientists to concepts in information visualization.

To use:

1. Clone or download this repository
2. cd into the repository
3. Call docker:

```
docker run -it --rm -v /outside_path/to/data/:/data -v $PWD:/home/neuro/test -p 8888:8888 satra/ibro-workshop-2017

```

4. inside docker, first download data if you haven't:

```
cd /data
datalad install -r -g ///workshops/nih-2017/ds000114
```

5. start jupyter:

```
jupyter-notebook --ip=*
```

6. open the url in a local browser.
