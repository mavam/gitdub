**gitdub** is a [github service web-hook][post-receive-hook] that converts a
changeset pushed to a github repository into exactly one email per change via
[git-notifier][git-notifier]. Unlike the existing github email hook, gitdub
sends out detailed diffs for each change.

Setup
=====

### Dependencies

  - Ruby 1.9
  - `gem install git sinatra`
  - [git-notifier][git-notifier]

### Installation
  
  1. `cp gitdub /path/to/dir/in/$PATH`
  2. `cp config.yml.example config.yml`
  3. `gitdub config.yml`

### Integration with github

  1. Navigate to a repository you own, e.g., `https://github.com/user/repo`
  2. Click on *Settings* on the top-right corner
  3. Click on *Service Hooks* in the left sidebar
  4. Select the first service called *WebHook URLs*
  5. Enter the URL to reach gitdub, e.g., `http://gitdub.mydomain.com:8888/`
  6. Click *Test Hook* to try whether github can reach you via 
  7. Click *Update Settings* to save your changes

Restricting Access
------------------

To prevent unauthorized access to the service, you can restrict the set of
allowed source IP addresses to github addresses, e.g., via iptables:

    iptables -A INPUT -m state --state NEW -m tcp -p tcp \
        -s 207.97.227.253,50.57.128.197,108.171.174.17 --dport 42042 -j ACCEPT

If that's not an option on your machine, you can also perform application-layer
filtering in gitdub by setting the following configuration option:

    allowed_sources: [207.97.227.253, 50.57.128.197, 108.171.174.17]


Licence
=======

Gitdub comes with a BSD license, please see COPYING for details.

[git-notifier]: http://www.icir.org/robin/git-notifier
[sinatra]: http://www.sinatrarb.com
[post-receive-hook]: https://help.github.com/articles/post-receive-hooks
