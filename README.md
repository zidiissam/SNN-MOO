# SNN-Hybrid Reference Environmental Selection Optimizer

This repository provides a Java implementation of **SNN-Hybrid-Reference-EnvSelection**, a hybrid multi-objective and many-objective optimization algorithm developed on top of the **MOEA Framework**.

The algorithm combines:

- Spiking Neural Network (SNN)-guided structured sampling
- NSGA-II-style elitist non-dominated environmental selection
- NSGA-III-style reference-vector niching for many-objective problems
- JADE/SHADE-like differential evolution search
- SBX crossover and polynomial mutation
- OMOPSO-style turbulence for stagnation recovery
- External nondominated archive management

The optimizer is designed for solving real-valued multi-objective and many-objective optimization problems where both convergence and diversity of the Pareto-front approximation are important.

---

## Main Features

- Hybrid evolutionary-neural search strategy
- Adaptive operator selection among SNN, DE, SBX, and turbulence operators
- Reference-vector-based environmental selection for three or more objectives
- Crowding-distance-based selection for two-objective problems
- External archive of nondominated elite solutions
- Rescue mechanism for stagnation handling
- Configurable population size, offspring size, archive size, neuron count, and reference-vector count
- Compatible with the MOEA Framework algorithm structure

---

## Algorithm Overview

The optimizer follows the general workflow:

1. Initialize the population using MOEA Framework initialization.
2. Evaluate the initial candidate solutions.
3. Apply elitist environmental selection.
4. Store nondominated solutions in an external archive.
5. Generate offspring using one of four operators:
   - SNN-guided sampling
   - JADE/SHADE-like current-to-pbest DE
   - SBX crossover with polynomial mutation
   - OMOPSO-style turbulence
6. Merge parent and offspring populations.
7. Apply non-dominated sorting.
8. Use crowding distance for two-objective problems.
9. Use reference-vector niching for many-objective problems.
10. Update the archive, membrane potentials, operator scores, and search parameters.
11. Trigger rescue injection if stagnation is detected.
12. Return the final nondominated archive.

---

## Repository Structure

Recommended structure:

```text
SNN-Hybrid-Reference-EnvSelection/
│
├── src/
│   └── main/
│       └── java/
│           └── org/
│               └── moeaframework/
│                   └── algorithm/
│                       └── SNNHybridReferenceEnvSelectionT.java
│
├── README.md
├── LICENSE
└── pom.xml

The Java file should remain under:

org.moeaframework.algorithm

because the class uses the package declaration:

package org.moeaframework.algorithm;
Requirements
Java 21 or later
MOEA Framework
Maven or Gradle
Eclipse IDE, IntelliJ IDEA, or any Java IDE
Maven Dependency

Add the MOEA Framework dependency to your pom.xml.

Example:

<dependencies>
    <dependency>
        <groupId>org.moeaframework</groupId>
        <artifactId>moeaframework</artifactId>
        <version>5.1</version>
    </dependency>
</dependencies>

If you use a different MOEA Framework version, update the version number accordingly.

Installation

Clone the repository:

git clone https://github.com/your-username/SNN-Hybrid-Reference-EnvSelection.git
cd SNN-Hybrid-Reference-EnvSelection

Build with Maven:

mvn clean package

Or import the project directly into Eclipse or IntelliJ IDEA.

Usage

The algorithm can be instantiated directly in a Java experiment by passing a MOEA Framework Problem object.

Example structure:

import org.moeaframework.algorithm.SNNHybridReferenceEnvSelectionT;
import org.moeaframework.core.NondominatedPopulation;
import org.moeaframework.problem.Problem;

public class RunExperiment {

    public static void main(String[] args) {

        Problem problem = ...; // Define or load your MOEA Framework problem

        SNNHybridReferenceEnvSelectionT algorithm =
                new SNNHybridReferenceEnvSelectionT(problem);

        while (!algorithm.isTerminated()) {
            algorithm.step();

            if (algorithm.getNumberOfEvaluations() >= 5000) {
                algorithm.terminate();
            }
        }

        NondominatedPopulation result = algorithm.getResult();

        System.out.println("Number of nondominated solutions: " + result.size());
    }
}

You may also register the algorithm in a custom MOEA Framework factory if you want to call it by name from an experiment pipeline.

Configurable Parameters

The implementation exposes several configurable parameters:

Parameter	Description	Default
populationSize	Number of parent solutions	48
offspringSize	Number of offspring generated per iteration	24
archiveSize	Maximum size of the external archive	120
neuronsPerVariable	Number of SNN neurons assigned to each decision variable	17
referenceVectorCount	Number of reference vectors used for many-objective niching	max(archiveSize, populationSize)

Example:

SNNHybridReferenceEnvSelectionT algorithm =
        new SNNHybridReferenceEnvSelectionT(problem);

algorithm.setPopulationSize(100);
algorithm.setOffspringSize(50);
algorithm.setArchiveSize(150);
algorithm.setNeuronsPerVariable(21);
algorithm.setReferenceVectorCount(150);
Internal Search Operators

The algorithm adaptively selects among four operators:

Operator	Purpose
SNN	Generates candidates using membrane potentials and archive-guided neural sampling
DE	Applies JADE/SHADE-like current-to-pbest differential evolution
SBX	Applies simulated binary crossover and polynomial mutation
Turbulence	Applies OMOPSO-inspired perturbation to recover diversity

Operator probabilities are updated according to survival and reward feedback during the search.

Environmental Selection

The selection mechanism depends on the number of objectives:

For two-objective problems, the algorithm uses:
fast non-dominated sorting
crowding-distance-based final-front selection
For three or more objectives, the algorithm uses:
fast non-dominated sorting
reference-vector association
PBI-based niching
niche occupancy balancing

This makes the method suitable for both multi-objective and many-objective optimization.

Archive Management

The algorithm maintains an external nondominated archive. The archive is used for:

elite leader selection
SNN-guided search
archive consensus learning
diversity preservation
rescue injection guidance

If the archive exceeds the maximum archive size, environmental selection is used to reduce it.

Stagnation Recovery

If the optimizer detects several iterations without accepted offspring, it activates a rescue stage. The rescue stage generates new candidates using:

turbulence perturbation
DE-based jump
opposition-based movement

This mechanism helps the population escape stagnation and recover diversity.

Suggested Experimental Setup

A typical setup for benchmark testing is:

Maximum evaluations: 5000
Population size: 100
Offspring size: 50
Archive size: 100–150
Neurons per variable: 17–21
Reference vectors: equal to or larger than population size

Recommended quality indicators:

Hypervolume
Generational Distance
Inverted Generational Distance
Spacing
Additive epsilon
Number of nondominated solutions
Runtime
Citation

If you use this implementation in your research, please cite the related paper or repository.

@article{snn_hybrid_reference_envselection,
  title   = {SNN-Hybrid Reference Environmental Selection for Multi- and Many-Objective Optimization},
  author  = {Your Name},
  journal = {To be added},
  year    = {2026}
}
License

This project is released for academic and research use.

Please add a suitable license file, such as:

MIT License
Apache License 2.0
GPLv3
Disclaimer

This implementation is intended for research and experimental benchmarking. Parameter tuning may be required depending on the benchmark problem, number of objectives, decision-variable dimension, and evaluation budget.

Contact

For questions, suggestions, or collaboration:

Author: Dr. Issam ZIDI
Email: zidi.issam@gmail.com
