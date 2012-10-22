About this manual
=================


Scope
-----

This manual describes the policy requirements for the Debian distribution. This includes the structure and contents of the Debian archive and several design issues of the operating system, as well as technical requirements that each package must satisfy to be included in the distribution.

This manual also describes Debian policy as it relates to creating Debian packages. It is not a tutorial on how to build packages, nor is it exhaustive where it comes to describing the behavior of the packaging system. Instead, this manual attempts to define the interface to the package management system that the developers have to be conversant with.[1]

The footnotes present in this manual are merely informative, and are not part of Debian policy itself.

The appendices to this manual are not necessarily normative, either. Please see Introduction and scope of these appendices, Appendix A for more information.

In the normative part of this manual, the words must, should and may, and the adjectives required, recommended and optional, are used to distinguish the significance of the various guidelines in this policy document. Packages that do not conform to the guidelines denoted by must (or required) will generally not be considered acceptable for the Debian distribution. Non-conformance with guidelines denoted by should (or recommended) will generally be considered a bug, but will not necessarily render a package unsuitable for distribution. Guidelines denoted by may (or optional) are truly optional and adherence is left to the maintainer's discretion.

These classifications are roughly equivalent to the bug severities serious (for must or required directive violations), minor, normal or important (for should or recommended directive violations) and wishlist (for optional items). [2]

Much of the information presented in this manual will be useful even when building a package which is to be distributed in some other way or is intended for local use only.


New versions of this document
-----------------------------

This manual is distributed via the Debian package debian-policy (packages.debian.org /debian-policy).

The current version of this document is also available from the Debian web mirrors at /doc/debian-policy/. ( www.debian.org /doc/debian-policy/) Also available from the same directory are several other formats: policy.html.tar.gz (/doc/debian-policy/policy.html.tar.gz), policy.pdf.gz (/doc/debian-policy/policy.pdf.gz) and policy.ps.gz (/doc/debian-policy/policy.ps.gz).

The debian-policy package also includes the file upgrading-checklist.txt.gz which indicates policy changes between versions of this document.


Authors and Maintainers
-----------------------

Originally called "Debian GNU/Linux Policy Manual", this manual was initially written in 1996 by Ian Jackson. It was revised on November 27th, 1996 by David A. Morris. Christian Schwarz added new sections on March 15th, 1997, and reworked/restructured it in April-July 1997. Christoph Lameter contributed the "Web Standard". Julian Gilbey largely restructured it in 2001.

Since September 1998, the responsibility for the contents of this document lies on the debian-policy mailing list. Proposals are discussed there and inserted into policy after a certain consensus is established. The actual editing is done by a group of maintainers that have no editorial powers. These are the current maintainers:

Russ Allbery

Bill Allombert

Andrew McMillan

Manoj Srivastava

Colin Watson

While the authors of this document have tried hard to avoid typos and other errors, these do still occur. If you discover an error in this manual or if you want to give any comments, suggestions, or criticisms please send an email to the Debian Policy List, debian-policy@lists.debian.org, or submit a bug report against the debian-policy package.

Please do not try to reach the individual authors or maintainers of the Policy Manual regarding changes to the Policy.


Related documents
-----------------

There are several other documents other than this Policy Manual that are necessary to fully understand some Debian policies and procedures.

The external "sub-policy" documents are referred to in:

File System Structure, Section 9.1.1

Virtual packages, Section 3.6

Menus, Section 9.6

Perl programs and modules, Section 11.9

Prompting in maintainer scripts, Section 3.9.1

Emacs lisp programs, Section 11.10

In addition to those, which carry the weight of policy, there is the Debian Developer's Reference. This document describes procedures and resources for Debian developers, but it is not normative; rather, it includes things that don't belong in the Policy, such as best practices for developers.

The Developer's Reference is available in the developers-reference package. It's also available from the Debian web mirrors at /doc/developers-reference/.

Finally, a specification for machine-readable copyright files is maintained as part of the debian-policy package using the same procedure as the other policy documents. Use of this format is optional.


Definitions
-----------

The following terms are used in this Policy Manual:

ASCII
The character encoding specified by ANSI X3.4-1986 and its predecessor standards, referred to in MIME as US-ASCII, and corresponding to an encoding in eight bits per character of the first 128 Unicode characters, with the eighth bit always zero.

UTF-8
The transformation format (sometimes called encoding) of Unicode defined by RFC 3629. UTF-8 has the useful property of having ASCII as a subset, so any text encoded in ASCII is trivially also valid UTF-8.
