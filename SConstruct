# Copyright (C) 2013 by Xiaolin (Charlene) Zang

import os

env = DefaultEnvironment()

build_variants = COMMAND_LINE_TARGETS
if len(build_variants) == 0:
   build_variants = ['build', 'lint']

if 'lint' in build_variants:
   SConscript('SConscript', variant_dir='lint', duplicate=1)

# Make a phony target to time stamp the last successful build
def PhonyTarget(target, source, action):
    env.Append(BUILDERS = { 'phony' : Builder(action = action) })
    AlwaysBuild(env.phony(target = target, source = source))

def write_build_info(target, source, env):
    os.system('date > build/last-build-info')

# NOTE: Although the creation of the time stamp file works, I can't quite
# explain how/why.  The SConscript file does not actually return anything
# after calling build_all().  But if I use the variable "builds" below to
# capture whatever the return value and then give it to the phony builder
# as the "source" parameter, it seems to work.
#
if 'build' in build_variants:
   builds = SConscript('SConscript', variant_dir='build', duplicate=1)
   PhonyTarget('build/write-build-info', builds, write_build_info)
