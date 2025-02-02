# IsaNet: Formalization of a Verification Framework for Secure Data Plane Protocols
README file accompanying 2022 AFP submission
The paper presenting this formalization is to appear in the Journal of Computer Security under the title ``IsaNet: A Framework for Verifying Secure Data Plane Protocols''.

This is a copy of https://www.isa-afp.org/entries/IsaNet.html, created on Oct 21, 2024.

================================================================================

authors:
           Tobias Klenze, ETH Zurich <tobias.klenze@inf.ethz.ch>
           Christoph Sprenger, ETH Zurich <sprenger@inf.ethz.ch>

version: JCSPaper.1.0
   This formalization is an extended and improved version over the formalization belonging to the CSF 2021 paper (Formal Development of Secure Data Plane Protocols, Klenze et al) with the version number CSF.1.0.
  
Isabelle version: 
  Isabelle/HOL 2021

In this README, we first give a guide to all of our supplementary material, all of our models, from abstract to concrete but parametrized to protocol instance. We then explain how to install and use Isabelle to inspect our models and validate the correctness of our proofs. Notice that for Isabelle, looking at the source files directly is not the most convenient way of inspecting proofs -- see the section "Inspecting our Isabelle/HOL proofs" below.

Guide to our Framework in Isabelle/HOL
--------------------------------------------------------------------------------
All_Protocols.thy includes all protocol instances and thus opening this file in Isabelle will load all other files.
The file generated/outline/session_graph.pdf shows the dependency graph of the different Isabelle theories that we have developed. Pure, Tools, HOL and HOL-Library belong to the Isabelle package and are not developed by us. The Event_Systems theory and the theory belonging to the Dolev-Yao attacker (Agents, Keys, Message) are also not part of our contribution. We now give a brief description of the theories, grouped into four categories:

Infrastructure:
  - Event_Systems: defines event systems as labeled transition systems, defines simulation relation and shows its soundness by proving that simulation implies trace inclusion.
  - Agents, Keys, Message: Dolev-Yao symbolic model
  - Tools, Take_While, Take_While_Update: Definitions of pfragment (pair-fragment, i.e. fragments lifted to a pair (ainfo, path)), the takeWhile functions used in the interface check and the updatable takeWhile function used in the cryptographic check.

Abstract and Parametrized Models:
  - Network_Model: Defines abstract hop field format, links, and what it means for hop fields to be interface-valid.
  - Parametrized_Dataplane_0: Our abstract model of a dataplane protocol, in which we prove detectability and path authorization as state invariants (and additionally as trace properties).
  - Parametrized_Dataplane_1: The intermediate model, in which we refine the abstract model by adding interface-validity checks. Messages from this model are mapped to messages of the abstract model by restricting the future path to the its longest interface-valid prefix (other fields of a message is unmodified).
  - Parametrized_Dataplane_2: The concrete, parametrized model, in which we refine the intermediate model down to a model with a Dolev-Yao adversary. This model is too abstract to prove the crux of the refinement: the simulation of the attacker event. We thus assume it in a locale.
  - Network_Assumptions: Has assumptions for directed and undirected settings of path authorization.
  - Parametrized_Dataplane_3_directed: Still parametrized, but concrete enough to prove the simulation of the attacker event. This is the model for the directed setting (for protocols like SCION, EPIC and Anapaya_SCION).
  - Parametrized_Dataplane_3_undirected: Still parametrized, but concrete enough to prove the simulation of the attacker event. This is the model for the undirected setting (for protocols like ICING).
  - Abstract_XOR: Theory for abstract modeling of exclusive-or.

Instance models:
  - SCION (directed): vanilla SCION, as described in the SCION book
  - SCION_variant (directed): minor modification to SCION protocol
  - EPIC_L1_BA (directed): EPIC Level 1 in the Basic Attacker Model
  - EPIC_L1_SA (directed): EPIC Level 1 in the Strong Attacker Model
  - EPIC_L2_SA (directed): EPIC Level 2 in the Strong Attacker Model
  - ICING (undirected): ICING protocol
  - ICING_variant (undirected): ICING protocol with some modifications to remove fields not strictly necessary to make the security mechanism work.
  - ICING_variant2 (undirected): ICING protocol with even more simplifications. This model removes the abstract hop field from the MAC key. While simpler, this introduces a problem: if an AS is present twice on the same path, both hop fields have the same MAC and thus cannot distinguish between the two hop fields. We add an assumption to our model that states that there can be at most one hop field per AS on a path (though the same hop field could be on the same path twice).
  - Anapaya_SCION (directed): Forwarding protocol proposed by Anapaya. Makes use of updatable uinfo fields and of XOR.

Example:
  - EPIC_L1_SA_Example: an instantiation of our most complex protocol (EPIC_L1_SA), to prove that all locale assumptions can be satisfied and the resulting event system is executable.

Inspecting our Isabelle/HOL proofs
--------------------------------------------------------------------------------
The files document.pdf and outline.pdf in the 'generated' directory contain our formalization with, and without proofs respectively, as single PDF documents. A more convenient, and interactive way of browsing theories is to use the Isabelle GUI:

Download Isabelle2021-1 here:
https://isabelle.in.tum.de/website-Isabelle2021-1/index.html

This includes two programs:
 - Isabelle2021-1: This starts the default Isabelle GUI (based on jEdit). It is the most convenient way of browsing the theories, as it allows inspecting the proof state and using Control+Click to jump to definitions and lemmas.
 - bin/isabelle: This is a command line tool. You may also build the logic images corresponding to the main session by typing the following command in this directory. Running this tool also shows the successful verification of our proofs. See also ROOT file.
 ```
isabelle build -v -b -D .
```
   

Loading the theory All_Protocols.thy into Isabelle2021-1 will load the entire formalization. Do Ctrl-Click on constants or lemmas to jump to their definition.

Statistics of formalization in Isabelle/HOL
--------------------------------------------------------------------------------

   149 infrastructure/Abstract_XOR.thy
    66 infrastructure/Agents.thy
   483 infrastructure/Event_Systems.thy
   147 infrastructure/Keys.thy
  1203 infrastructure/Message.thy
   262 infrastructure/Take_While.thy
   239 infrastructure/Take_While_Update.thy
   160 infrastructure/Tools.thy
  2709 total
Total # of definitions:   9
Total # of lemmas:        332

    94 Network_Model.thy
   374 Parametrized_Dataplane_0.thy
   226 Parametrized_Dataplane_1.thy
   783 Parametrized_Dataplane_2.thy
    72 Network_Assumptions.thy
   652 Parametrized_Dataplane_3_directed.thy
   263 Parametrized_Dataplane_3_undirected.thy
  2464 total
Total # of definitions:   47
Total # of lemmas:        87

   321 instances/SCION.thy
   317 instances/SCION_variant.thy
   395 instances/EPIC_L1_BA.thy
   406 instances/EPIC_L1_SA_Example.thy
   437 instances/EPIC_L1_SA.thy
   464 instances/EPIC_L2_SA.thy
   567 instances/Anapaya_SCION.thy
   330 instances/ICING.thy
   258 instances/ICING_variant.thy
   267 instances/ICING_variant2.thy
    31 All_Protocols.thy
  3793 total
Total # of definitions:   67
Total # of lemmas:        280




