# perl6-concurrent-file-find
concurrent File::Find for Perl 6

# SYNOPSIS

```
use v6;
use Concurrent::File::Find;

find(%*ENV<HOME>
    , :extension('txt', {.contains('~')}) # ends in .txt or ends in something that contains a ~
    , :exclude('covers') # exclude any path that contains covers, both for files and directories
    , :exclude-dir('.') # exclude any directory-path that contains a . 
    , :file # return file paths
    , :!directory # don't return directory paths
    , :symlink # return symlink paths
    , :recursive # be recursive
    , :max-depth(5) # but not deeper then 5 directories deep
    , :follow-symlink # follow symlinks (no loop detection yet)
    , :keep-going # on error (no access, stale symlink, etc.), keep going
    , :quiet # don't report errors on STDERR
).elems.say; # count how many files and symlinks we got

sleep 10;

my @l := find-simple(%*ENV<HOME>, :keep-going, :!no-thread); # binding to avoid eagerness

for @l {
    @l.channel.close if $++ > 5000; # hard-close the channel after 5000 found files
    .say if $++ %% 100 # print every 100th file
}
```

# DESCRIPTION

## Exceptions

### `X::IO::NotADirectory`

Try do get the content a path that is not a directory.

### `X::IO::CanNotAccess`

Access to a directroy is denied by the OS.

### `X::IO::StaleSymlink`

We where ment to return or follow a symlink that does exists but got no target.

### `X::Paramenter::Exclusive`

Named arguments where used together that are mutual exclusive.

# CAVEATS

Loop detection is not supported yet. As soon as there are portable versions for
readlink and/or stat, loop detection will be added. Until now avoid
`:follow-symlink` or use `:max-depth`.
