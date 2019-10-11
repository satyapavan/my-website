# my-website-sourcecode
Source code for the github pages site https://SatyaPavan.github.io
Build logs can be found at https://travis-ci.org/satyapavan/my-website-sourcecode


## For Sub module configuration

```
-- initialize submodule configuration
git submodule init 
-- update your submodule --remote fetches new commits in the submodules
-- and updates the working tree to the commit described by the branch
git submodule update --remote
```

## For Deleting a submodule

```
git submodule deinit themes/hugo-future-imperfect-slim
git rm themes/hugo-future-imperfect-slim
git commit -m "Removed submodule "
rm -rf .git/modules/themes/hugo-future-imperfect-slim/
```
