Here's how I generated this repository from the commit history of the main titan repo.

Run these commands from a directory containing the titan repo.

I stole /tmp/rewrite_parent.rb from this mailing list posting:
http://www.spinics.net/lists/git/msg177988.html

```bash
mkdir titan-experimental && cd titan-experimental && git init && git pull ../titan master

git filter-branch
    --prune-empty \
    --index-filter 'prefixes=`git ls-files | grep -Ev "^(titan-infinispan|titan-hazelcast)/" | cut -d/ -f1 | sort | uniq`; [ -n "$prefixes" ] && git rm --cached -r --ignore-unmatch $prefixes' \
    --parent-filter /tmp/rewrite_parent.rb HEAD

# Sanity check -- if there are any differences, then something went wrong.
for x in infinispan hazelcast; do
    diff -x '.classpath' -x '.settings' -x '.project' -qrN titan-$x ../titan/titan-$x && \
        echo "titan-$x matches ../titan/titan-$x exactly (OK)"
done
```

Copy and create doc files:

```bash
cp ../LICENSE.txt .
echo '### Experimental Titan modules and extensions' > README.md
```

Create a new Github repository.  In Github's repository creation wizard,
uncheck the option to initialize with a README.  We want a bare repo.

Now configure the new GH repo as a remote and push:

```bash
git remote add origin git@github.com:thinkaurelius/titan-experimental.git
git push origin -u master
```
