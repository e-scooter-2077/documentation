# Contributing to the documentation


## Preview the results
You can use Docker to install and run the [website generation tool](https://github.com/ldeluigi/markdown-docs).

### Download the docker image
`docker pull deloo/markdown-docs`

### Generate the website
`docker run -e "WORKSPACE=/workspace" -v "<Absolute-Path-To-The-Repo>:/workspace" markdown-docs "./" "dest/"`  

!> Clean the output directory before each new generation with `rmdir dest /s /q` (on Windows).

!> Remember to replace `<Absolute-Path-To-The-Repo>`.

### See the result
`explorer dest\index.html`
