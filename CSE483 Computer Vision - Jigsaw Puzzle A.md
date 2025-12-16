###CSE483 Computer Vision - Jigsaw Puzzle Assembly Project
Overview
This project automatically assembles jigsaw puzzles using classical computer vision techniques only - no machine learning or deep learning. The system processes puzzle images, extracts individual pieces, and reassembles them using edge matching and optimization algorithms.
Key Point: Everything is done using traditional CV methods like CLAHE, edge detection, gradient analysis, and optimization.

Quick Start
Requirements
bashpip install opencv-python numpy matplotlib scikit-image


### Run in Google Colab
1. Upload your puzzle dataset (ZIP files)
2. Run Milestone 1 cells (preprocessing)
3. Run Milestone 2 cells (assembly)
4. Check output folders for results



## Project Structure

dataset/
├── puzzle_2x2/        # 2×2 puzzle images
├── puzzle_4x4/        # 4×4 puzzle images
├── puzzle_8x8/        # 8×8 puzzle images
└── correct/           # Ground truth images

processed_output/      # Milestone 1 outputs
├── pieces/            # Individual puzzle pieces
├── enhanced/          # CLAHE enhanced images
├── edges/             # Edge detection results
└── edge_profiles/     # Gradient data

ms2_results_*/         # Milestone 2 outputs
└── {puzzle_id}_result.png  # Assembly visualizations

Milestone 1: Image Preprocessing
What It Does
Prepares puzzle images for assembly by extracting and analyzing individual pieces.
Pipeline Steps
1. Split Grid

Takes complete puzzle image
Divides into individual pieces based on grid size (2×2, 4×4, or 8×8)

2. CLAHE Enhancement

Improves contrast for better edge detection
Handles varying lighting conditions

pythonclahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))
enhanced = clahe.apply(grayscale_image)
3. Background Removal

Separates puzzle piece from background
Uses adaptive thresholding

4. Edge Detection

Finds piece boundaries using Canny edge detector
Applies Gaussian blur first to reduce noise

5. Extract Edge Features

For each edge (top, bottom, left, right):

Gradient magnitude (edge strength)
Color profile (RGB values)
Local texture information



[Visual: Original → Enhanced → Edges → Extracted Pieces]

Milestone 2: Puzzle Assembly
Three Different Solvers
We use different algorithms optimized for each puzzle size:
2×2 Solver: Beam Search

Tests multiple possible arrangements simultaneously
Keeps top candidates at each step
Scores pieces based on:

Edge gradient matching
Color similarity
Local texture similarity



Speed: ~5 seconds


4×4 Solver: Frontier Growing

Places pieces one at a time, starting from a seed
Chooses next piece based on how well it fits neighbors
Uses LAB color space for better color matchings

Key Innovation: Compares seam costs (how well edges align)
pythoncost = pixel_difference + gradient_continuity
Speed: ~30 seconds

[Visual: Frontier expansion animation]

8×8 Solver: Multi-Seed + Refinement

Tries 24 different starting pieces
Uses hybrid cost function:

Seam pixel differences
Gradient continuity
Sobel edge profiles


Refines result by swapping adjacent pieces

Speed: ~3 minutes

[Visual: Seed selection → Assembly → Refinement]

How Edge Matching Works
The Core Idea
Puzzle edges that fit together should have complementary shapes:

One edge goes "in" (concave)
The other edge goes "out" (convex)

Our Approach

Extract edge profiles using Sobel gradients
Normalize the gradient values (0 to 1 scale)
Compare edge A with inverted edge B
Score based on how well they complement each other

python# If edges fit: convex edge + concave edge ≈ flat line
magA_normalized = (magA - min) / (max - min)
magB_normalized = (magB - min) / (max - min)

error = mean((magA_normalized - (1.0 - magB_normalized))²)
[Visual: Edge profiles showing complementary matching]

Results
Performance Summary
Puzzle SizeAccuracyProcessing Time2×295%5 seconds4×478%30 seconds8×868%3 minutes
Output Visualization
Each result shows three images:

Left: Our assembled puzzle
Middle: Ground truth (correct solution)
Right: Correctness map (green = correct placement, red = wrong)

[Visual: Example output showing all three panels]

Key Technical Decisions
Why CLAHE?

Better than regular histogram equalization
Handles local lighting variations
Prevents noise amplification

Why LAB Color Space?

Matches human color perception
More robust to lighting changes than RGB
Improves matching accuracy by ~15%

Why Frontier Growing?

Better than pure greedy (which fails often)
Doesn't require ML like genetic algorithms
Good balance of speed and accuracy

Why Multiple Seeds for 8×8?

Single starting point can get stuck
Testing 24 different starts finds better solutions
Small computational cost for big accuracy gain



# Milestone 2: Assembly
# For 2×2:
run_ms2("processed_output/puzzle_2x2", "ms2_results_2x2", size=(2,2))

# For 4×4:
run_ms2_4x4("processed_output/puzzle_4x4", "ms2_results_4x4")

# For 8×8:
run_ms2_8x8_improved("processed_output/puzzle_8x8", "ms2_results_8x8")

Team Contributions
MemberTasks[Name]Preprocessing pipeline, CLAHE[Name]2×2 solver, beam search[Name]4×4 solver, LAB matching[Name]8×8 solver, optimization[Name]Testing, documentation

References
Classical CV Techniques:

CLAHE: Pizer et al. (1987)
Canny Edge Detection: Canny (1986)
LAB Color Space: CIE 1976 standard

Puzzle Assembly:

Cho et al. (2010) - Patch Transform
Pomeranz et al. (2011) - Automated Solver
Gallagher (2012) - Unknown Orientation

Libraries:

OpenCV Documentation
NumPy Reference


Academic Integrity
This project follows Ain Shams University's academic integrity policies. All work is original and properly cited.

Course: CSE483 Computer Vision, Fall 2025
Institution: Ain Shams University, Faculty of Engineering