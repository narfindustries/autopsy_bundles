# Process to exchange Java Bundles with Translator(s) #

When using @Messages annotations style, here is how to get the generated .properties files as well as the _lang.properties files all together for translation.

## Setup Repo for Bundles ##
0. create new dir/repo to store the generated properties files that is outside of the autopsy repo
0. create the empty repo in github named "autopsy_bundles" and clone it, so it sits next to the autopsy dir.

## The process ##
1. build autopsy
-- the 'build/classes/' dir in each module will contain all of the created classes and the .properties files

2. create a dir structure in the autopsy_bundles dir that only has the .properties files for a specific module, and only those from that module's build dir

for root modules: (replace ImageGallery with module name)
$ find autopsy/ImageGallery/build/classes/org/sleuthkit/autopsy/imagegallery/  -iname "Bundle*.properties" -exec cp --parents {} autopsy_bundles/ \;

for Core modules: (replace corecomponents with module name)
$ find autopsy/Core/build/classes/org/sleuthkit/autopsy/corecomponents/ -iname "Bundle*.properties" -exec cp --parents {} autopsy_bundles/ \;

4. commit and push those changes in the autopsy_bundles repo

5. when translated, copy the foreign properties files back into the src/ tree.

for root modules: (replace ImageGallery with module name)
$ cd autopsy_bundles/autopsy/ImageGallery/build/classes/
$ find . -iname "Bundle_ja.properties" -exec cp --parents {} ../../../../../autopsy/ImageGallery/src/ \;

for Core modules: (replace corecomponents with module name)
$ cd autopsy_bundles/autopsy/Core/build/classes/org/sleuthkit/autopsy/corecomponents 
$ find . -name "Bundle_ja.properties" -exec cp --parents {} /cygdrive/c/Users/nick/basis_dev_root/autopsy/Core/src/org/sleuthkit/autopsy/corecomponents/ \;

6. commit new files to autopsy repo and push

