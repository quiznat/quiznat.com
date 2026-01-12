**The Royal Game of Gods and Kings**

In Irish mythology, Fidchell wasn't just a game; it was divine technology. The Tuatha Dé Danann, the godlike race who ruled Ireland before mortals, attributed its invention to Lugh, their principal deity. The name itself, "Fidchell" (pronounced FID-kell), translates to "Wooden Wisdom."

The game appears throughout Celtic literature with an almost mystical reverence. In the Táin Bó Cúailnge, King Conchobar mac Nessa of Ulster devoted a full third of his daily royal schedule to playing fidchell. Not governing. Not warfare. Fidchell.

The Welsh called it Gwyddbwyll, and their stories are stranger still. In the Mabinogion, King Arthur plays gwyddbwyll with Owein, son of Urien, while their armies clash outside the hall. As pieces move on the board, soldiers fall on the battlefield. The game mirrors reality, or perhaps controls it.

This wasn't for commoners. Fidchell was exclusive to nobility and druids and was played on boards of "white gold" with gold and silver pieces. At sacred assemblies like the Great Assembly of Uisnech, it was used for divination.

The original rules were lost to history. What we built is based on the most rigorous modern reconstruction, adapted for the web with a strong AI opponent.

Play it here: https://fidchell.quiznat.com/

**The Game**

Fidchell uses a circular board of seven concentric rings, connected by radial lines. This creates 109 intersection points for placing pieces, 108 around the rings, plus a center point where the King stone sits.

The ring structure matters. Movement flows along the lines: you can slide outward or inward along a radial, or around a ring's circumference. Pieces slide until blocked. No jumping.

This geometry creates natural chokepoints and forces players to think about connectivity differently than on a square grid. The center is powerful but exposed. The outer ring is the finish line, but hard to defend.

Placement Phase: Players alternate placing stones (27 each). White moves first. During this phase, you're building infrastructure, establishing positions, threatening captures, and blocking your opponent's paths.

Movement Phase: Once all 54 stones are placed, the game shifts. Now you slide pieces along the board lines. The King stone at the center, dormant during placement, becomes active and can be claimed by either player for their path.

Capture: Sandwich an enemy stone between two of yours (custodian capture), and it's removed from the board. Captures grant extra turns, and these chain pull off a double capture, and you keep going. This creates explosive tactical sequences where a single move can cascade into board-clearing combinations.

Victory: Connect a continuous path of your stones from the center King to any point on the outer ring (ring 7). The path can twist through the rings and radials however you like, as long as it's unbroken.

The game has clean strategic layers:

* Placement is an investment. Every stone you place is a permanent positioning. Misplace early, and you're fighting uphill.  
* Captures create tempo. The extra-turn mechanic means aggressive play compounds. A player who finds a capture chain can turn a losing position around.  
* The King is shared infrastructure. Both players need to connect through the center. This forces interaction; you can't just turtle on your side of the board.  
* Ring geometry creates natural tension. Inner rings have fewer points (ring 1 has just 4 positions). Outer rings have more space but are farther from the goal. You're constantly trading control for reach.

**Technical Implementation**

The entire game runs in the browser with zero dependencies. Pure HTML, CSS, and JavaScript. The codebase separates concerns cleanly:

* GameState: an immutable state container. Complete board as a 109-position array. Clone-on-write for all mutations. Serialize/deserialize for persistence and Web Worker communication.  
* GameRulesEngine: pure functions for game logic. Validates placements, executes moves, detects captures, and checks win conditions via BFS pathfinding.  
* BoardRenderer:  SVG rendering with incremental updates. Only redraws what changed. Handles all animations.  
* NegamaxEngine:  The AI. More on this below.  
* FidchellGame:  Orchestration layer. Handles clicks, manages turn flow, coordinates between engine and renderer.

This separation made development fast. I could iterate on AI without touching rendering, tweak animations without breaking game logic.

Each point on the board knows its:

*  Ring number (1-7, or 0 for center)  
*  Spoke position (which radial line it sits on)  
*  X/Y coordinates for rendering  
*  Adjacent points (for movement and pathfinding)

Adjacency is precomputed at board initialization. Points connect radially (same spoke, adjacent rings) and circumferentially (same ring, adjacent spokes). This makes move generation trivial: iterate over adjacencies and check for blockers.

Win detection uses breadth-first search from the center, exploring only the current player's stones and checking whether any path reaches ring 7\.

**The AI: Negamax with All the Trimmings**

Fidchell has a high branching factor. In the opening, there are more than 100 legal placements. Minimax would drown. Monte Carlo Tree Search (MCTS) was the first attempt; it works but requires thousands of simulations to perform well, and the results still felt random even after tuning.

Negamax with alpha-beta pruning was the answer. Same idea as minimax (evaluate positions, look ahead, assume optimal play) but with aggressive pruning of branches that can't possibly affect the result.

Iterative Deepening: Start with depth-1 search, then depth-2, then depth-3, up to the time limit. Each iteration informs the next through move ordering. If time runs out, we have the best move from the previously completed depth.

Transposition Table: Cache evaluated positions. Fidchell positions repeat, same board state reachable through different move orders. The TT stores: exact scores, upper bounds, lower bounds, best moves found. Keyed by Zobrist hash of the position.

Move Ordering: The order in which you search moves dramatically affects pruning efficiency. We sort by:

*   TT move (best move from the previous search of this position)  
*   Captures, ordered by MVV-LVA (Most Valuable Victim \- Least Valuable Attacker)  
*   Killer moves (moves that caused beta cutoffs at this ply in sibling nodes)  
*   History heuristic (moves that have historically been good)

Good move ordering means more cutoffs, which means a deeper search in the same time.

Quiescence Search: Don't stop searching mid-capture sequence. When the main search reaches its depth limit, quiescence search continues to extend captures only until the position is "quiet." This prevents the horizon effect: the AI does not walk into a lost capture because it cannot see farther.

When the search bottoms out, we need to score the position. The evaluation considers:

* Material: Raw piece count difference  
* Path Progress: How close each player is to completing a winning path (measured by the highest ring reached in a connected path from the center)  
* Near-Win Bonus: Massive bonus for being one move from victory  
* Center Control: Bonus for controlling points adjacent to the King  
* Path Length: The length of each player's longest connected path toward the edge

These weights were tuned through self-play. A genetic algorithm ran 30 generations of tournaments (1680 games total), evolving weight combinations. The fittest survived.

**Building with Claude Code**

This project was built entirely in Claude Code, Anthropic's AI-powered development environment. Some observations from the process:

Iterative refinement of game logic. I could describe rule edge cases in natural language ("what happens if a capture creates a new capture opportunity?") and get correct implementations. The source material, three markdown files with the canonical rules, served as ground truth. When behavior seemed wrong, I'd point Claude at the source material, and it would self-correct.

AI architecture exploration. I started with MCTS, realized it wasn't working, and pivoted to Negamax. Claude handled both implementations and the migration between them. Explaining why MCTS wasn't suitable for Fidchell's branching factor helped it recommend the right alternative.

Animation polish. The path-following animation system came from describing the problem ("pieces teleport, but they should follow the board tracks") and iterating on solutions. Claude understood the board geometry and generated the pathfinding code to animate through intermediate points.

Difficulty tuning. The AI weights and depth limits need playtesting feedback. Claude could implement changes instantly, but knowing what to change required playing games and feeling where the difficulty curve broke.

Source material interpretation. The Unicorn Garden reconstruction has some ambiguous edge cases. Deciding how to interpret "the King is dead during placement" or "captures grant extra turns" required human judgment about what would make the best game, not just what the text literally said.

The most productive pattern was:

1. Describe the feature or bug at a high level  
2. Let Claude explore the codebase and propose an approach  
3. Refine the approach through discussion  
4. Let Claude implement  
5. Test, find issues, iterate

This worked better than either extreme (fully specifying everything upfront, or giving zero direction and hoping for the best).

**Lessons Learned**

Circular boards are underexplored. The seven-ring geometry creates strategic patterns that don't exist on rectangular grids. The chokepoints at inner rings, the expansion possibilities at outer rings, and the shared center emerge from the topology, not from rules bolted on top.

Historical games deserve modern implementations. Fidchell was played for over a thousand years by some of history's most strategic minds. The fact that it's obscure today is an accident of history, not a judgment on its quality. There are probably dozens of lost games worth resurrecting.

AI difficulty is about feel, not just strength. A depth-6 search beats depth-3, but that's not why expert mode is fun to play against. It's because depth-6 finds moves that surprise you, that teach you something about the position. The best AI opponents make you better at the game.

**Try It**

The game is live at https://fidchell.quiznat.com/

Start with Easy to learn the mechanics. Work up to Hard to test your tactical vision. Try expert mode for a real challenge.

If you consistently beat expert mode, let me know. I'll make depth 7\.

\---  
Built with Claude Code. Source reconstruction based on Unicorn Garden's Fidchell research (https://www.unicorngarden.com/fidchell/).