# neuroviz

A set of notebooks to introduce neuroscientists to concepts in information visualization.

Requirements:

docker

To use:

1. Clone or download this repository and unzip if necessary.

2. `cd` into the repository directory

3. Call docker: Make sure to replace `[/outside_path/to/data/]` with a pointer to a location on your local computer where data can be stored.

```
docker run -it --rm -v [/outside_path/to/data/]:/data -v $PWD:/home/neuro/test \
      -p 8888:8888 satra/ibro-workshop-2017
```

4. Inside Docker, first download data if you haven't. If you have mounted the external path properly, you only need to do this once. This will take some time.

```
cd /data
datalad install -r -g ///workshops/nih-2017/ds000114
cd
```

5. Start Jupyter:

```
$ jupyter-notebook --ip=*
```
to start jupyter with Xvfb enabled for the Mayavi notebook do:

```
$ Xvfb :1 +extension GLX -screen 0 1024x780x24 &
$ DISPLAY=:1 jupyter-notebook --ip=*
```

The `$` represents your shell prompt inside the container.

6. Open the url in a local browser. 


### To recreate the docker image:

You will first need to download the cifti data and untar it.

```
curl -L https://www.dropbox.com/s/6povj9s96m0phc7/cifti.tgz?dl=0 | tar zx -
```

Then generate the Dockerfile

```
docker run --rm kaczmarj/neurodocker generate -b neurodebian:stretch-non-free -p apt \
--install tree git-annex-standalone vim emacs-nox nano less ncdu tig git-annex-remote-rclone \
--instruction "RUN curl -sL https://deb.nodesource.com/setup_6.x | bash -" \
--install nodejs build-essential \
--instruction "ENV LC_ALL=C.UTF-8" \
--instruction "RUN apt-get update && apt-get install -yq xvfb mesa-utils" \
--user=neuro \
--miniconda python_version=3.6 \
            conda_install="jupyter jupyterlab pandas matplotlib scikit-learn seaborn altair traitsui apptools configobj reprozip reprounzip vtk" \
            env_name="neuro" \
            pip_install="nilearn datalad mayavi" \
--instruction "RUN bash -c \"source activate neuro && pip install --pre --upgrade ipywidgets pythreejs \" " \
--instruction "RUN bash -c \"source activate neuro && pip install  --upgrade https://github.com/maartenbreddels/ipyvolume/archive/23eb91685dfcf200ee82f89ab6f7294f9214db8c.zip && jupyter nbextension install --py --sys-prefix ipyvolume && jupyter nbextension enable --py --sys-prefix ipyvolume \" " \
--instruction "RUN bash -c \"source activate neuro && conda install jupyter_contrib_nbextensions \" " \
--instruction "RUN bash -c \"source activate neuro && pip install --upgrade https://github.com/nipy/nibabel/archive/master.zip \" " \
--instruction "COPY cifti-data /cifti-data" \
--instruction "USER root" \
--instruction "RUN chmod -R a+r /cifti-data " \
--install graphviz \
--instruction "USER neuro" \
--instruction "RUN bash -c \"source activate neuro && jupyter nbextension enable rubberband/main && jupyter nbextension enable exercise2/main && jupyter nbextension enable spellchecker/main && conda install -yq bokeh scikit-image traits \" " \
--instruction "RUN bash -c \"source activate neuro && pip install --upgrade https://github.com/nipy/nipype/tarball/master https://github.com/INCF/pybids/archive/master.zip nipy duecredit \" " \
--instruction "RUN bash -c \"source activate neuro && python -c 'from nilearn import datasets; haxby_dataset = datasets.fetch_haxby()' \" " \
--workdir /home/neuro \
--no-check-urls > Dockerfile
```

Then build it:

```
docker build -t mycontainer .
```
