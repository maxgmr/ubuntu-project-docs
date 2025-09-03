(npm-updating-rust)=

rustc: Updating Rust
====================

This guide details the process of creating a new versioned ``rustc`` Ubuntu package for a new upstream Rust release.

.. attention::

   This is not a guide for updating your system's Rust toolchain. This guide is intended only for Ubuntu toolchain package maintainers seeking to add new Rust versions to the Ubuntu archive.

Background
----------

The ``rustc`` :term:`source package`, which provides :term:`binary packages <binary package>` for the Rust toolchain, is a `versioned` package. This means that a new source package is created for every Rust release (e.g., `rustc-1.83`_ and `rustc-1.84`_).

.. _rustc-1.83: https://launchpad.net/ubuntu/+source/rustc-1.83
.. _rustc-1.84: https://launchpad.net/ubuntu/+source/rustc-1.84

These packages are maintained largely in order to support building other Rust packages in the :term:`Ubuntu archive`. Rust developers seeking to work on their own Rust programs typically use the `rustup snap`_ instead.

.. _rustup snap: https://snapcraft.io/rustup

Unlike most packages, we maintain ``rustc`` separately from :term:`Debian`, meaning that we get the updated :term:`source code` directly from its :term:`upstream` source.

High-Level Summary of the Update Process
----------------------------------------

A typical ``rustc`` update goes through the following steps:

#. The source code of the new Rust version is downloaded from upstream, overwriting the old upstream source code.
#. The existing package :term:`patches <patch>` are refreshed so they apply properly onto the new Rust source code.
#. Unnecessary vendored dependencies are pruned.
#. The upstream Rust source is re-downloaded with the new list of files to yank out, overwriting the source code once again, but with the unnecessary files removed.
#. Any issues causing the new Rust package build to fail are fixed.

Substitution Terms
------------------

From now on, the documentation will contain certain terms within angle brackets which must be replaced with the actual value that applies to you.

As an example, let's assume you're updating ``rustc-1.84`` (upstream version ``1.84.0``) to ``rustc-1.85`` (upstream version ``1.85.1``) for Noble Numbat:

* ``<X.Y>``: The short Rust version you're updating `to`.

    Example: ``1.85``

* ``<X.Y.Z>``: The long Rust version you're updating `to`.

    Example: ``1.85.1``

* ``<X.Y_old>``: The short Rust version you're updating `from`.

    Example: ``1.84``

* ``<X.Y.Z_old>``: The short Rust version you're updating `from`.

    Example: ``1.84.0``

* ``<release>``: The target :term:`Ubuntu release` adjective.

    Example: ``noble``

* ``<lpuser>``: Your :term:`Launchpad` username. This is also used to refer to your personal Launchpad Git repository's remote name.
* ``<foundations>``: Your local Git remote name for the Foundations `rustc Git repository`_.

.. _rustc Git repository: https://git.launchpad.net/~canonical-foundations/ubuntu/+source/rustc
