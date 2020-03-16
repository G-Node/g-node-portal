# G-Node Portal

The address g-node.github.io/g-node-portal is referenced in a publication so the deployed page from this repository should stay online.  There will be no further updates to this project.  The front page now includes a banner that redirects users to [GIN](https://gin.g-node.org).

---

Note:
* The themes needed for compilation of this documentation by Sphinx should be pulled from a different repository (https://github.com/apleonhardt/gnode-sphinx-themes) and stored in source/_themes/
* The compiled html files should be pushed to gh-pages branch of this repository


Instructions:
1) git clone git@github.com:G-Node/g-node-portal.git g-node-portal && cd g-node-portal
2) git clone https://github.com/apleonhardt/gnode-sphinx-themes source/_themes
3) mkdir -p build/html && git clone -b gh-pages git@github.com:G-Node/g-node-portal.git build/html
4) make html
5) git add -A && git ci -m "commit message" && git push #push changes to source code
6) cd build/html
7) git add -A && git ci -m "commit message" && git push #push changes to HMTL pages
