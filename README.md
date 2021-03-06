
This project is to help RPM-packagers build .spec files that can pull sources 
directly from git. The usage is something like this (inside .spec file):

    URL: https://github.org/api/v3/user/projectname/repository/archive?ref=v%{version}
    
    %prep
    %if 0%{?setup_from_git:1}
    %setup_from_git -U %{url} -S %{S:0} -n %{name}-%{version}
    %else
    # setup compatible with github-generated tarball
    %setup -c -T
    tar axf %{S:0} --strip-components 1
    %endif

To make it work, the included macro _must be appended_ to either `~/.rpmmacros` or 
one of the site macro files (see `man rpmbuild`). (If rpmbuild ever handles a reasonable
method of extension or directory-based includes, that requirement will change.)

For public repositories, no additional effort is required, provided the URL works
(you can test with `curl` or `wget` or a web browser). For repositories that are private, 
an access token is required. Set your token inside the environment variable `PRIVATE_TOKEN`
and that token will be passed along as an HTTP header `PRIVATE-TOKEN`, with a `-` instead of 
`_` (underscore).

This has been tested with a gitlab-provided repository, running v3 of the API. 

