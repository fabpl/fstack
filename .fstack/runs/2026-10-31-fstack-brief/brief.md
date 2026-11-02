---
run: 2026-10-31-fstack-brief
kind: brief
author: fplanchette
date: 2026-10-31
---

# Original brief (verbatim, French)

fstack est un plugin claude (exclusivement claude). il fournit un skill f-chief (pour chief of staff) qui se comporte comme mon bras droit. il est en fable eleve.

sa mission numero 1 : créer et maintenir un wiki dans le projet ou se trouve la conversation. ce wiki est en fichier markdown et sert de mémoire sur les décisions, actions et informations pertinentes sur le projet. le frontmatter permet notamment de définir des relations.

sa mission numero 2, comprendre la demande utilisateur et y répondre de maniere efficace en déléguant le plus possible les taches. il va donc reprendre les fondamentaux des skills de pocock sur la partie compréhension : grill-with-docs, to-spec et to-issues permettent justement de s'aligner sur la compréhension de l'intention et de produire un spec et des issues pertinentes pour y répondre. une fois ces élements produits, on passe a la realisation : f-chief doit invoquer des sous agents, en parallele si possible, pour mener a bien l'implementation. implement-spec, avec tdd et code-review sont sans doute à adapter. f-chief va donc orchester cela et devoir demander à ce que les agents redige leurs rapports dans un endroits commun de facon a gérer une mémoire entre agents : leurs decisions, leurs observations sont donc importants à sauvegarder et pourront également servir à produire les entrées du wiki et fournir le rapport final. Les conventions de dossiers, fichiers sont donc à déterminer et figer. Un point d'attention : tout cela doit etre économe : les réponses des agents doivent etre succincts mais précis.

sa mission numero 3 : si la demande inital n'est pas de produire du code mais simplement répondre à une question, il doit etre en mesure de le faire de maniere rapide, efficace et précise.
