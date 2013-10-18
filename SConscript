# Copyright (C) 2013 by Carnegie Mellon University.

import os
import re
import fnmatch
import filecmp

env = DefaultEnvironment()

#### Decisions are made here.  The rest of the file are function defintions.
def main():
    if Dir('.').path == 'lint':
        lint_all()
    if Dir('.').path == 'build':
        build_all()

####  All build targets are defined here

def build_all():
    env.Program(['src/hello-world.c'])
    return

#### Lint related stuff

def find_files():
    patterns = ['*.cpp', '*.c', '*.hpp', '*.h']
    for root, dirs, files in os.walk('..'):
        if root.startswith('../.git') or root.startswith('../build') or \
           root.startswith('../lint') :
            continue
        for basename in files:
            if any(fnmatch.fnmatch(basename, p) for p in patterns) :
                filename = os.path.join(root, basename)
                yield filename

# The body of self-defined builder .Lint
def lint_action(target, source, env):
    # target and source are lists of file objects
    src = str(source[0])      # in source tree
    to_lint = 'lint/' + src   # in lint tree

    # For no apparent reason, Google's cpplint.py 
    # (http://google-styleguide.googlecode.com/svn/trunk/cpplint/cpplint.py)
    # refuses to lint .hpp files.
    # cpplint.py only lints files named as .cc, .cpp and .h. (why?)
    # Rename non-conforming file to fool cpplint.py into linting them.
    # Copy the file to lint tree if necessary.
    #
    if fnmatch.fnmatch(src, '*.hpp'):
        to_lint += '.cpp'
    if fnmatch.fnmatch(src, '*.c'):
        to_lint += '.cc'
    if (not (os.path.isfile(to_lint) and filecmp.cmp(src, to_lint))) :
        os.system('cp %s %s' % (src, to_lint))

    # It's best to pass cpplint.py without any special-casing.
    # But the forgiveness is needed sometimes.
    #
    lint_options = '--filter=-readability/streams' + \
               ',-runtime/references' + \
               ',-whitespace/newline'
    lintcmd = 'python cpplint.py %s %s' % (lint_options, to_lint)
    print lintcmd
    status = os.system(lintcmd)
    if (status == 0):
        datecmd = 'date > ' + str(target[0])
        print datecmd
        os.system(datecmd)
    return status

def lint_all():
    env.Append(BUILDERS = {'Lint' : Builder(action = lint_action)})
    for filename in find_files():
        # file are found while the current dir "lint".  Therefore
        # filename is in the form of ../src/xxx.  Remove the first dot, so
        # to keep the target in lint dir
        #
        target = filename[1:] + '.lint'
        env.Lint(target, filename)

main()

# to google-style a cpp/hpp file: "astyle -a --indent=spaces=2 <file>"
