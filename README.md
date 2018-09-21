gitdub
======

**gitdub** is a [github web-hook][post-receive-hook] that converts a
changeset pushed into one email per change via [git-notifier][git-notifier].
Unlike the existing github email hook, gitdub sends out detailed diffs for each
change.

Setup
=====

Dependencies
------------

  - git (>= 1.7.12)
  - Ruby (>= v1.9)
  - `gem install sinatra`
  - [git-notifier][git-notifier] (master branch required)

Installation
------------

  1. `cp gitdub /path/to/dir/in/$PATH`
  1. `cp config.yml.example config.yml`
  1. `gitdub config.yml`

Integration with github
-----------------------

  1. Navigate to a repository you own, e.g., `https://github.com/user/repo`
  1. Click on *Settings* in the right sidebar
  1. Click on *Webhooks & Services* in the left sidebar
  1. Click on *Add Webhook*
  1. Enter the URL to reach gitdub, e.g., `http://gitdub.mydomain.com:8888/`
  1. Set the content type to `application/x-www-from-urlencoded`
  1. Select the radio button *Just the `push` event*
  1. Click on the green *Add Webhook* button

Customizing
===========

The [YAML](http://www.yaml.org) configuration file contains the list of
repositories that gitdub tracks. The first section (`gitdub:`) specifies global
options, such as the interfaces gitdub should bind to and ports to listen on. 
Moreover, you can control the behavior of the first chunk of data. When setting
`silent_init:` to true, gitdub will only fast-forward to the current commit and
begin mailing diffs after the next push (or after hitting the *Test Hook*
button). Otherwise gitdub sends exactly one email per commit since the first
commit in the repository.

The second section (`notifier:`) describes the behavior of git-notifier. Here you
can the configure a global sender of the emails (`from:`), the receivers
(`to:`), and the prefix of the email subject (`subject:`).

The third section (`github:`) contains a list of github repository entries,
where each entry must at least contain an `id` field. If an item does not
contain any further options, the globals from the `notifier` section apply.
However, in most cases it makes sense to override the globals with
repository-specific information, e.g.:

    notifier:
      # The email sender. (Can be overriden for each repository.)
      from: 'Sam Sender <foo@host.com>'

      # The email subject prefix. (Can be overriden for each repository.)
      subject: '[git]'

    github:
      - id: mavam/gitdub
        subject: '[git/gitdub]'    # Overrides global '[git]' subject prefix.
        from: vallentin@icir.org   # Overrides global sender.
        to: [vallentin@icir.org]   # Overrides global receivers.

      - id: mavam/.*
        from: mavam                # Overrides global sender.

Note the regular expression in the second entry. This enables the configuration
of entire sets of repositories. Since gitdub processes the list sequentially in
order of definition, only settings from the first match apply. For example,
appending an entry for `mavam/foo` would never match.

Restricting Access
------------------

To prevent unauthorized access to the service, you can restrict the set of
allowed source IP addresses to github addresses, e.g., via iptables:

    iptables -A INPUT -m state --state NEW -m tcp -p tcp \
        -s 207.97.227.253,50.57.128.197,108.171.174.178 --dport 42042 -j ACCEPT

If that's not an option on your machine, you can also perform application-layer
filtering in gitdub by setting the following configuration option:

    allowed_sources: [207.97.227.253, 50.57.128.197, 108.171.174.178]


Licence
=======

Gitdub comes with a BSD license, please see COPYING for details.

[git-notifier]: http://www.icir.org/robin/git-notifier
[sinatra]: http://www.sinatrarb.com
[post-receive-hook]: https://help.github.com/articles/post-receive-hooks
