---
title: 'Perforce P4V use notes'
toc: true
date: "2015-12-07T20:59:25+00:00"
---
# Perforce at a glance

I've never gotten touch with Perforce, even heard about its name. Strange! Now because some versions of my company's classic product are hosted on Perforce, so I need to know it from the beginning.

Basically, Perfoce is an commercial Version Control Systerm, like IBM Clearcase. It supports industry-level version control in the form of client-server architecuture. So the central depository exists for sure. It provides all sorts of robust features modern VCS all provides. But from the comments of my colleagues, I know it supports very heavy-weight branching.

P.S: `P4` is the acronym of Perforce by pronunciation, just like Peer-to-Peer as P2P or Log-for-Java as Log4j.

# Perforce concepts

Whenever we get to know some new products or tools, we need to know its own slang. That's comprised of tons of well-defined words.

- depository: A tree of depositories are the remote views on the server
- workspace: It's the directory where the local copy of the files locate
- check out: Some resources should be modified exclusively, so you can use `check out` to prevent these resource from being modified by others.
- branch: a branch of version that can be viewed and manipulated seperately.
- merge: the same as that in other VCSs

# Client tool: P4V

<to-be-continued>


