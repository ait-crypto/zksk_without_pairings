####
zksk, but without bplib
####

.. start-description-marker-do-not-remove

This library is a fork of the Zero-Knowledge Swiss Knife, without the (non-critical) library "bplib" for pairings, which is no longer maintained and causes installation to fail on modern systems.
What follows is taken from the REAMDE of the original library.

Zero-Knowledge Swiss Knife: Python library for prototyping composable zero-knowledge proofs in the
discrete-log setting.

--------------------------------------------------------------------------------------------------

Let's say Peggy commits to a secret bit and wants to prove to Victor in zero knowledge that she
knows this bit—that is, without revealing it. In Camenisch-Stadler notation, we can write:

.. image:: https://raw.githubusercontent.com/spring-epfl/zksk/master/images/bit_proof_stmt.svg?sanitize=true
   :alt: PK{ (r): (C = rH) ∨ (C - G = rH) }

To implement this zero-knowledge proof, Peggy will run:

.. code-block:: python

    from zksk import Secret, DLRep
    from zksk import utils

    # Setup: Peggy and Victor agree on two group generators.
    G, H = utils.make_generators(num=2, seed=42)
    # Setup: generate a secret randomizer.
    r = Secret(utils.get_random_num(bits=128))

    # This is Peggy's secret bit.
    top_secret_bit = 1

    # A Pedersen commitment to the secret bit.
    C = top_secret_bit * G + r.value * H

    # Peggy's definition of the proof statement, and proof generation.
    # (The first or-clause corresponds to the secret value 0, and the second to the value 1. Because
    # the real value of the bit is 1, the clause that corresponds to zero is marked as simulated.)
    stmt = DLRep(C, r * H, simulated=True) | DLRep(C - G, r * H)
    zk_proof = stmt.prove()


Victor will receive the commitment ``C`` and ``zk_proof`` from Peggy, and run this to verify the
proof:

.. code-block:: python

    from zksk import Secret, DLRep

    # Setup: get the agreed group generators.
    G, H = utils.make_generators(num=2, seed=42)
    # Setup: define a randomizer with an unknown value.
    r = Secret()

    stmt = DLRep(C, r * H) | DLRep(C - G, r * H)
    assert stmt.verify(zk_proof)

Victor is now convinced that Peggy knows the committed bit.

--------------------------------------------------------------------------------------------

===========================
Documentation and materials
===========================

+----------------+--------------------------------------------------------------------+
| Docs           | https://zksk.readthedocs.io                                        |
+----------------+--------------------------------------------------------------------+
| Academic paper | https://arxiv.org/abs/1911.02459 —                                 |
|                | theoretical details                                                |
+----------------+--------------------------------------------------------------------+

.. end-description-marker-do-not-remove

> **Warning.** Please don't use this software for anything mission-critical. It is designed for
quick protyping of privacy-enhancing technologies, not production use.

--------------------------------------------------------------------------------------------

.. end-citing-do-not-remove
