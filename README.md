
{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {},
   "outputs": [],
   "source": [
    "import matplotlib.pyplot as plt\n",
    "%matplotlib inline\n",
    "import numpy as np\n",
    "\n",
    "from qiskit import IBMQ, BasicAer\n",
    "from qiskit.providers.ibmq import least_busy\n",
    "from qiskit import QuantumCircuit, ClassicalRegister, QuantumRegister, execute\n",
    "\n",
    "from qiskit.tools.visualization import plot_histogram\n",
    "from IPython.display import display, Math, Latex"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Quantum Computing and the Deutsch-Jozsa Algorithm"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Why Quantum Computing ?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "In 1981, the Nobel laureate Richard Feynman asked, “What kind of computer are we going to use to simulate physics?”\n",
    "\n",
    "*Nature isn’t classical, dammit, and if you want to make a simulation of Nature, you’d better make it quantum mechanical, and by golly it’s a wonderful problem, because it doesn’t look so easy.*\n",
    "\n",
    "Richard Feynman speech can be used to see how powerful quantum computing could be to let us understand more about our universe, since quantum physics try to explain and understand how our universe is built from the deepest subatomic dimensions to huge macroscopic phenomena. What can lead as to ask ourselves if our classical compurters are going to be able to solve and deal with problems that nature by essence shows to be a quantum complex behavior, that most likely are exponential problems for our classical computers, like the nitrogen-fixing process on agriculture, where dealing with several atoms interections, as the number of atoms grows the dimesion of our problem grows exponencially, what is impossible for a classical computer to solve nowdays.\n",
    "\n",
    "So the question is, how quantum computers could solve these kind of problems some day ? With a different model of treating information closer with how our universe and nature behave, from the deepest levels and beyond."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Classical Bit vs Qubit"
   ]
  },
  {
   "attachments": {
    "image.png": {
     "image/png": "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"
    }
   },
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Our classical computers, those that we've in home, desktops, notebooks, cellphones all of them in the deepest level stores the information as different sequences of **0s** and **1s**, so as **0100100100** where each entrie is either 0 or either 1. What makes a huge difference when we compare it with how the quantum computers stores information and work with it, where a state 0 or 1 can be seen as a unitary vector state $|\\psi\\rangle$ in a Hilbert space $\\mathcal{H}$ or a Qubit, in the computational basis, defined as:\n",
    "\n",
    "\n",
    "$$\\left|\\psi\\right\\rangle = \\alpha\\left|0\\right\\rangle + \\beta\\left|1\\right\\rangle$$\n",
    "\n",
    "$$|\\alpha|^2 + |\\beta|^2 = 1$$\n",
    "\n",
    "\n",
    "![image.png](attachment:image.png)\n",
    "\n",
    "\n",
    "\n",
    "\n",
    "\n",
    "Where the scalars alpha and beta are used to see the amplitude of each state, or more generally, its probability to colapse over its respective state $|0\\rangle$ or $|1\\rangle$, which is like we could have all possible states at the same time, but we can only measure and see a small piece of this huge information."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Quantum Superpostion and Entanglement"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "As we're dealing with quantum behavior some pretty important features of quantum mechanics that are used in quantum computing are the **Quantum Superposition** and the **Quantum Entanglement**. If were given to us a state $\\psi$, when neither of its constants, lets say for example $\\alpha$ and $\\beta$, are null, we say that this state is in **superpostion**. More generally, which means that before a potential measure a phisical system is partially over all possible existants theoretical states.\n",
    "\n",
    "$$\n",
    "|\\psi\\rangle = a_0|0\\rangle + a_1|1\\rangle + \\dots + a_{n-1}|n-1 \\rangle = \\sum_{i=0}^{n-1}a_i|i\\rangle\n",
    "$$\n",
    "\n",
    "$$\n",
    "\\sum_{i = 0}^{n-1} |a_i|^2 = 1\n",
    "$$\n",
    "\n",
    "However when we measure it, it colapses to a unique state, so its like nature is doing a massive work, but it is willing to share with us just a small piece of it, a hint, for us to solve some very difficult problems.\n",
    "\n",
    "The space of these vector states can be seen by tensor product of this system states, so if we've **n** components we can write those as \n",
    "\n",
    "$$\n",
    "\\begin{align*}\n",
    "|\\Psi\\rangle &= |\\psi_0\\rangle \\otimes |\\psi_1\\rangle \\otimes \\dots \\otimes |\\psi_{n-1}\\rangle\\\\\n",
    "             &= |\\psi_0\\rangle |\\psi_1\\rangle \\dots |\\psi_{n-1}\\rangle\\\\\n",
    "             &= |\\psi_0 \\psi_1 \\dots \\psi_{n-1}\\rangle\n",
    "\\end{align*}\n",
    "$$\n",
    "\n",
    "If a state can not be writen by the tensor product of other states we've that this states is **entangled**, so as the wellknown Bell-State\n",
    "\n",
    "\n",
    "$$\n",
    "\\begin{align*}\n",
    "|\\Psi\\rangle = \\frac{|00\\rangle + |11\\rangle}{\\sqrt{2}}\n",
    "\\end{align*}\n",
    "$$\n",
    "\n",
    "\n",
    "Where if we measure one of the Qubits we know exactly the state of the other, even if the other Qubit is in the other side of the galaxy, with is forbidden by the theory of relativity, since it would be faster then the speed of light, which is known as the *EPR Paradox*."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Programming with Quantum Gates"
   ]
  },
  {
   "attachments": {
    "image.png": {
     "image/png": "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"
    }
   },
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "While in Classical Computers works by applying simple boolean operators like (NOT, NAND, XOR, AND, OR), Quantum Computers works Quantum Gates, which mathematically speaking are unitary operators, that manipulates our state over the bloch sphere, so let $\\mathcal{U}$ be our unitary operator and $|\\psi\\rangle$ our qubit.\n",
    "\n",
    "$$\n",
    "UU^\\dagger = I\n",
    "$$\n",
    "\n",
    "$$\n",
    "|\\psi\\rangle \\rightarrow \\mathcal{U}|\\psi\\rangle\n",
    "$$\n",
    "\n",
    "$$\n",
    "|||\\psi\\rangle|| = ||\\mathcal{U}|\\psi\\rangle|| = 1\n",
    "$$\n",
    "\n",
    "In this context the gates that we'll use are those that work in an unique qubit, therefore our operators will be defined by $M_{2x2}$ matrices over a state $|\\psi\\rangle = \\alpha|0\\rangle + \\beta|1\\rangle$, and some of those operators are the Pauli Matrices, which besides its importance all over quantum computing, they are going to have a great importance to the understanding of the Deutsch-Jozsa Algorithm.\n",
    "\n",
    "\n",
    "$$\n",
    "\\sigma_I = I = \\begin{bmatrix}\n",
    "                1 & 0 \\\\\n",
    "                0 & 1\n",
    "               \\end{bmatrix}\n",
    "$$\n",
    "\n",
    "$$\n",
    "\\sigma_X = X = \\begin{bmatrix}\n",
    "                0 & 1 \\\\\n",
    "                1 & 0\n",
    "               \\end{bmatrix}\n",
    "$$\n",
    "\n",
    "$$\n",
    "\\sigma_Y = Y = \\begin{bmatrix}\n",
    "                0 & -i \\\\\n",
    "                i & 0\n",
    "               \\end{bmatrix}\n",
    "$$\n",
    "\n",
    "$$\n",
    "\\sigma_Z = Z = \\begin{bmatrix}\n",
    "                1 & 0 \\\\\n",
    "                0 & -1\n",
    "               \\end{bmatrix}\n",
    "               \\\\\n",
    "$$\n",
    "\n",
    "![image.png](attachment:image.png)\n",
    "\n",
    "\n",
    "As we already settled superposition is a important feature in quantum computing, how we can solve problems over multiple states at the same time, so if we've a state $|0\\rangle$ or $|1\\rangle$ and want to put it on superposition we can apply we one of the most common and important quantum gates, the Hadarmard Gate, given by the matrix\n",
    "\n",
    "$$\n",
    "\\begin{align*}\n",
    "H &= \\frac{1}{\\sqrt{2}} \\begin{bmatrix}\n",
    "                       1 & 1\\\\\n",
    "                       1 & -1\n",
    "                       \\end{bmatrix}\\\\\\\\\n",
    "  &= \\frac{|0\\rangle + |1\\rangle}{\\sqrt{2}}\\langle0| + \\frac{|0\\rangle - |1\\rangle}{\\sqrt{2}}\\langle1|\n",
    "\\end{align*}\n",
    "$$\n",
    "\n",
    "\n",
    "Hence, if we apply this matrix on the states $|0\\rangle$ or $|1\\rangle$ we'll get the states $|+\\rangle$ and $|-\\rangle$ respectively\n",
    "\n",
    "\n",
    "\n",
    "$$\n",
    "\\begin{align*}\n",
    "\\\\H|0\\rangle = \\frac{1}{\\sqrt{2}} \\begin{bmatrix}\n",
    "                                   1 & 1\\\\\n",
    "                                   1 & -1\n",
    "                                \\end{bmatrix} \n",
    "                                %\n",
    "             \\begin{bmatrix}\n",
    "             1 \\\\\n",
    "             0\n",
    "             \\end{bmatrix}\n",
    "             =\n",
    "             \\frac{|0\\rangle + |1\\rangle}{\\sqrt{2}}\n",
    "             =\n",
    "             |+\\rangle\n",
    "\\\\\\\\H|1\\rangle = \\frac{1}{\\sqrt{2}} \\begin{bmatrix}\n",
    "                                   1 & 1\\\\\n",
    "                                   1 & -1\n",
    "                                \\end{bmatrix} \n",
    "                                %\n",
    "             \\begin{bmatrix}\n",
    "             0 \\\\\n",
    "             1\n",
    "             \\end{bmatrix}\n",
    "             =\n",
    "             \\frac{|0\\rangle - |1\\rangle}{\\sqrt{2}}\n",
    "             =\n",
    "             |-\\rangle\n",
    "\\end{align*}\n",
    "$$\n",
    "\n",
    "Which are the resultant states after we put them on superposition. And since $H$ = $H^\\dagger$, when we apply $HH^\\dagger|\\psi\\rangle = HH^\\dagger|\\psi\\rangle = |\\psi\\rangle $, it restores it to the original input state."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## The Deutsch-Jozsa Algorithm"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The Deutsch-Jozsa Algorithm was one of the first algorithms to show a improvement over the classical solution to find whether a function is balanced or constant. Even though the algorithm does not has a important application in real life it show us in very simple way how this new kind of computing can improve our classical computers algorithm solutions.\n",
    "\n",
    "The functions that will be considerend in this algorithm are those that maps a input $\\{0, 1\\}^{n}$ to an output $\\{0, 1\\}$, i.e, $\\   \\mathcal{f}:\\ \\{0, 1\\}^n \\mapsto \\{0, 1\\}$ , and a function is called baleced if exactly half of its inputs give us an output equal to **0** and the other half equals **1**.\n",
    "\n",
    "$$\n",
    "\\begin{align*}\n",
    "Constant:\\ & f(0, 0) = 1 && f(0, 1) = 1  && f(1, 0) = 1 && f(1, 1) = 1 \\\\\n",
    "Constant:\\ & f(0, 0) = 0 && f(0, 1) = 0  && f(1, 0) = 0 && f(1, 1) = 0 \\\\\n",
    "Balanced:\\ & f(0, 0) = 1 && f(0, 1) = 0  && f(1, 0) = 1 && f(1, 1) = 0 \\\\\n",
    "Balanced:\\ & f(0, 0) = 0 && f(0, 1) = 1  && f(1, 0) = 0 && f(1, 1) = 1 \n",
    "\\end{align*}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "However, even being the \"Hello World\" of the Quantum Algorithms, the The Deutsch-Jozsa Algorithm is not as simple as it looks like. Therefore, as we already discussed the quantum circuits works by applying several unitary matrices operations over a quantum state on the computational basis."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$\n",
    "|0\\rangle = \\begin{bmatrix}\n",
    "                1 \\\\\n",
    "                0\n",
    "            \\end{bmatrix}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$\n",
    "|1\\rangle = \\begin{bmatrix}\n",
    "                0 \\\\\n",
    "                1\n",
    "            \\end{bmatrix}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We can apply our operations over several qubits, i.e, over the tensor product of simpler, in order to get more complex systems and inputs. Therefore we can introduce the Deutsch-Jozsa Algorithm Circuit."
   ]
  },
  {
   "attachments": {
    "image.png": {
     "image/png": "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"
    }
   },
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![image.png](attachment:image.png)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Firstly** we can itialize our register as the tensor product of **n** $|0\\rangle$ states and one $|1\\rangle$ that will be used to give us what is called a kick-back, therefore:"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$\n",
    "\\begin{align*}\n",
    "    |\\psi_0\\rangle & =  |0\\rangle^{\\otimes n} \\otimes |1\\rangle\\\\\n",
    "                   & =  |0\\rangle^{\\otimes n} |1\\rangle\n",
    "\\end{align*}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The **second** step is to put our state in superposition, by applying Hadarmard gates in our input as it follows:"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$\n",
    "\\begin{align*}\n",
    "    |\\psi_1\\rangle & =  H^{\\otimes n}|0\\rangle^{\\otimes n} \\otimes H|1\\rangle\\\\\n",
    "                   & =  H^{\\otimes n}|0\\rangle^{\\otimes n} H|1\\rangle\\\\\n",
    "                   & =  |+\\rangle^{\\otimes n}|-\\rangle\\\\\n",
    "                   & = {\\frac{1}{\\sqrt{2^n}}}\\sum_{x \\in \\{0, 1\\}^{n}} |x\\rangle\\bigg(\\frac{|0\\rangle - |1\\rangle}{\\sqrt2}\\bigg)\n",
    "\\end{align*}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "After that, the next step is to apply the $U_f$ oracle, which is the main function in our circuit that give us if the our input function is balanced or constant, but this result will not be observable yet. "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$\n",
    "\\begin{align*}\n",
    "    |\\psi_2\\rangle \n",
    "                   & = {\\frac{1}{\\sqrt{2^n}}}\\mathrm{U_f}\\bigg(\\sum_{x \\in \\{0, 1\\}^{n}} |x\\rangle\\bigg(\\frac{|0\\rangle - |1\\rangle}{\\sqrt2}\\bigg)\\bigg)\n",
    "\\end{align*}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The function will not change the first **n** qubits, but the second one will be change as it follows:\n",
    "\n",
    "$$\n",
    "\\begin{align*}\n",
    "    |\\psi_2\\rangle \n",
    "                   & = {\\frac{1}{\\sqrt{2^n}}}\\sum_{x \\in \\{0, 1\\}^{n}} |x\\rangle\\bigg(\\frac{|0 \\oplus f(x)\\rangle - |1 \\oplus f(x)\\rangle}{\\sqrt2}\\bigg)\\\\\n",
    "                   & \\equiv {\\frac{1}{\\sqrt{2^n}}}\\sum_{x \\in \\{0, 1\\}^{n}} (-1)^{f(x)} |x\\rangle\\bigg(\\frac{|0\\rangle - |1\\rangle}{\\sqrt2}\\bigg) & \\text{The kickback give us this formulation}\\\\\n",
    "                   & \\equiv {\\frac{1}{\\sqrt{2^n}}}\\sum_{x \\in \\{0, 1\\}^{n}} (-1)^{f(x)} |x\\rangle &\\text{            Ignoring the second qubit}\\\\\n",
    "\\end{align*}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now that we already have our $U_f$ oracle function output for the first **n** qubits remains to us only to apply the Hadamard Gate on it and to measure the result. The general formulation for applying the Hadarmard gate on n qubit on superposition is given by"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$\n",
    "|\\psi_3\\rangle = \\frac{1}{2^n}\\sum_{x \\in \\{0, 1\\}^n}(-1)^{f(x)}\\sum_{z \\in \\{0, 1\\}^n} (-1)^{\\langle x, z\\rangle}|x\\rangle\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Then, when $f(x)$ is balance $z = |00000 \\dots 0\\rangle$ the inner procuct sum will cancel each other term by term, which will give us that the amplitude to find the result on the state $|0000 \\dots 0\\rangle$ is null. Otherwise, we have a constant function and the amplitude of $|0000 \\dots 0\\rangle$ after the result on the measurement is either 1 or -1, which is a 1 probability of finding it on the state $|0000 \\dots 0\\rangle$."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Example"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Lets do a small example to see how it work properly for one random function in the required format, so as \n",
    "\n",
    "$$\n",
    "    f(0, 0) = 0\\\\\n",
    "    f(0, 1) = 1\\\\\n",
    "    f(1, 0) = 1\\\\\n",
    "    f(0, 0) = 0\n",
    "$$\n",
    "\n",
    "We know that this is a balanced function, since it is a pretty small example, but the algorithm still blinded for it.\n",
    "\n",
    "We will need three Qubits in our input, where there first two will be equal to $|0\\rangle$ and the third one will be $|1\\rangle$."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$\n",
    "\\begin{align*}\n",
    "    |\\psi_0\\rangle & =  |0\\rangle^{\\otimes 2} \\otimes |1\\rangle\\\\\n",
    "                   & =  |0\\rangle^{\\otimes 2} |1\\rangle\\\\\n",
    "                   & =  |00\\rangle|1\\rangle\n",
    "\\end{align*}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now we must apply the Hadarmard Gates on it as it follow \n",
    "\n",
    "$$\n",
    "\\begin{align*}\n",
    "    |\\psi_1\\rangle & =  H^{\\otimes 2}|00\\rangle H|1\\rangle\\\\\n",
    "                   & = |+\\rangle^{\\otimes 2}|-\\rangle\\\\\n",
    "                   & = \\bigg(\\frac{|0\\rangle + |1\\rangle}{\\sqrt{2}}\\bigg)\\otimes\\bigg(\\frac{|0\\rangle + |1\\rangle}{\\sqrt{2}}\\bigg)\\otimes\\bigg(\\frac{|0\\rangle - |1\\rangle}{\\sqrt{2}}\\bigg)\\\\\n",
    "                   & = \\frac{1}{2}\\bigg(|00\\rangle + |01\\rangle + |10\\rangle + |11\\rangle\\bigg)\\otimes\\bigg(\\frac{|0\\rangle - |1\\rangle}{\\sqrt{2}}\\bigg)\\\\\n",
    "                   & = \\frac{1}{2}\\bigg(|00\\rangle + |01\\rangle + |10\\rangle + |11\\rangle\\bigg)\\bigg(\\frac{|0\\rangle - |1\\rangle}{\\sqrt{2}}\\bigg)\n",
    "\\end{align*}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now that we've our state on superpostion we must apply the $U_f$ function on the state $|\\psi_1\\rangle$, so that\n",
    "\n",
    "$$\n",
    "\\begin{align*}\n",
    "|\\psi_2\\rangle & = \\frac{1}{2}\\bigg(|00\\rangle + |01\\rangle + |10\\rangle + |11\\rangle\\bigg)U_f\\bigg(\\bigg(\\frac{|0\\rangle - |1\\rangle}{\\sqrt{2}}\\bigg)\\bigg)\\\\\n",
    "               & = \\frac{1}{2\\sqrt{2}} \\sum_{x \\in \\{0, 1\\}^2} |x\\rangle \\bigg(|0 \\oplus f(x)\\rangle - |1 \\oplus f(x)\\rangle\\bigg)\\\\\n",
    "               & = \\frac{1}{2\\sqrt{2}}\\bigg[|00\\rangle \\bigg(|0\\rangle - |1\\rangle\\bigg) - |01\\rangle \\bigg(|0\\rangle - |1\\rangle\\bigg) - |10\\rangle \\bigg(|0\\rangle - |1\\rangle\\bigg) + |11\\rangle \\bigg(|0\\rangle - |1\\rangle\\bigg)\\bigg]\n",
    "\\end{align*}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now as we did at the explanation above we will ignore the last Qubit and evaluate our circuit only by the first **n** Qubits.\n",
    "\n",
    "$$\n",
    "  |\\psi_2\\rangle = \\frac{1}{2}\\bigg[|00\\rangle - |01\\rangle - |10\\rangle + |11\\rangle\\bigg]\n",
    "$$\n",
    "\n",
    "Then remain to us only to apply the Hadamard Gate on this state to get our result, in order to make it simpler we will use the matrix notation of the Hadamard $H^{\\otimes 2}$ to get the computation result.\n",
    "\n",
    "$$\n",
    "\\begin{align*}\n",
    "    |\\psi_3\\rangle & =  H^{\\otimes 2}|\\psi_2\\rangle\\\\ \n",
    "                & = \\begin{bmatrix}\n",
    "                      1 & 1 & 1 & 1\\\\\n",
    "                      1 & -1 & 1 & -1\\\\\n",
    "                      1 & 1 & -1 & -1\\\\\n",
    "                      1 & -1 & -1 & 1\n",
    "                      \\end{bmatrix}%\n",
    "                      \\frac{1}{2}\n",
    "                      %\n",
    "                     \\begin{bmatrix}\n",
    "                      1\\\\\n",
    "                      -1\\\\\n",
    "                      -1\\\\\n",
    "                      1\n",
    "                      \\end{bmatrix}\\\\\n",
    "                 & = |11\\rangle\n",
    "\\end{align*}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Where the measurement on the first two Qubits will be strictly different then $|00\\rangle$, therefore our function is balanced, as expected."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Implemention"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now that we understand how the algorithm works and saw a small example we can use the Qiskit library to simulate it and observe the results, comparing it with our theoretical explanations. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {},
   "outputs": [],
   "source": [
    "'''\n",
    "RANDOM EXAMPLE BASED ON IBM\n",
    "QISKIT TUTORIAL EXAMPLE\n",
    "reference: https://community.qiskit.org/textbook/ch-algorithms/deutsch-josza.html\n",
    "'''\n",
    "\n",
    "#n is the number of elements needed in the function input\n",
    "n = 5\n",
    "\n",
    "#define the oracle type, i.e, if our function is constant or balanced\n",
    "oracle = \"b\"\n",
    "\n",
    "#if is balanced we define which of the vector 2**n will held\n",
    "#the algorithm result\n",
    "if oracle == \"b\":\n",
    "    b = np.random.randint(1,2**n) \n",
    "    \n",
    "#if it's constant we set randomly if the results are 0 or 1\n",
    "if oracle == \"c\":\n",
    "    c = np.random.randint(2)\n",
    "\n",
    "#set n_qbits as the number of quantum register on the input\n",
    "#and the classical register as n_bits\n",
    "n_qbits = QuantumRegister(n+1)\n",
    "n_bits = ClassicalRegister(n)\n",
    "\n",
    "#build the circuit based on it\n",
    "djAlgCircuit = QuantumCircuit(n_qbits, n_bits)\n",
    "\n",
    "#as we need a |1> qbit to do the kickback in our circuit\n",
    "#we apply the X operator on the last |0> qbit to flip its\n",
    "#entrie and change it to |1>\n",
    "djAlgCircuit.x(n_qbits[n])\n",
    "\n",
    "#Apply this in order to viasualize our oracle on the \n",
    "#circuit plot\n",
    "\n",
    "#Apply the Hadarmard gates on the n qbits and put them\n",
    "#on the superposition state\n",
    "djAlgCircuit.h(n_qbits)    \n",
    "\n",
    "djAlgCircuit.barrier()\n",
    "\n",
    "#If it is constants just flip the value of the last qbit to 0 if c is one,\n",
    "#Apply the identity otherwise\n",
    "#If it is balanced we will shift the bits and apply the CNOT operator at the\n",
    "#marked position b and the last qbit, it works like our $|y \\oplus f(x)>$\n",
    "if oracle == \"c\": \n",
    "    if c == 1:\n",
    "        djAlgCircuit.x(n_qbits[n])\n",
    "    else:\n",
    "        djAlgCircuit.iden(n_qbits[n])\n",
    "else:  \n",
    "    for i in range(n):\n",
    "        if (b & (1 << i)):\n",
    "            djAlgCircuit.cx(n_qbits[i], n_qbits[n])\n",
    "\n",
    "\n",
    "djAlgCircuit.barrier()\n",
    "\n",
    "#Apply the Hadamard gates to get our result\n",
    "for i in range(n):\n",
    "    djAlgCircuit.h(n_qbits[i])\n",
    "\n",
    "#Measure the amplitude of the first n qbits\n",
    "for i in range(n):\n",
    "    djAlgCircuit.measure(n_qbits[i], n_bits[i])\n",
    "    \n",
    "backend = BasicAer.get_backend('qasm_simulator')\n",
    "shots = 1024\n",
    "results = execute(djAlgCircuit, backend=backend, shots=shots).result()\n",
    "answer = results.get_counts()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAArUAAAFlCAYAAAD1dhDzAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAADl0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uIDIuMS4xLCBodHRwOi8vbWF0cGxvdGxpYi5vcmcvAOZPmwAAIABJREFUeJzs3XlcVPX+P/DXwLDjxhC44JogOAKKG4YCbkWm0jUlzd1MROxa1k29dF2y3C5Xrbx5sXKpXK5AJl/Tq3gVytQuroVLaES4IOKCCiqyzO8PfkyOLHPQGT7nMK/n4+HjgZ8558xr5O2HN2c+54xKp9PpQERERESkYFaiAxARERERPSk2tURERESkeGxqiYiIiEjx2NQSERERkeKxqSUiIiIixWNTS0RERESKx6aWiIiIiBSPTS0RERERKR6bWiIiIiJSPDa1RERERKR4bGqJiIiISPHY1BIRERGR4rGpJSIiIiLFY1NLRERERIrHppaIiIiIFI9NLREREREpHptaIiIiIlI8NrVEREREpHhsaomIiIhI8djUEhEREZHisaklIiIiIsVjU0tEREREisemloiIiIgUTy06AFF9dfbs2RofX7VqFaZPn17jNt7e3qaMRDJirD4A1ggRUW3wTC2RIP/85z9FRyCZY40QEUnHppaIiIiIFI9NLREREREpHptaIkESEhJERyCZY40QEUnHppaIiIiIFI9NLZEgw4cPFx2BZI41QkQkHZtaIiIiIlI83qe2nnpjY90/58rRdf+cRGR6IuYP4MnnEJVKZZogtaDT6er8OYmoajxTSyRIdHS06Agkc6wRIiLp2NQSCWLsk6KIWCNERNKxqSUSJDg4WHQEkjnWCBGRdGxqiQTJy8sTHYFkjjVCRCQdm1oiIiIiUjw2tUSCdOzYUXQEkjnWCBGRdLylF5EgiYmJoiOQzLFGzKdNmzbo2bMn/P390bBhQzx48AAZGRk4evQojh07htLS0kr7zJs3DyqVCvPnz6/7wERkFJvaGty6dQuzZs3C119/jYKCAnTp0gVLly5F7969RUejemDu3Ll47733RMcgGWONmN6IESPw+uuvo0+fPtVuc+HCBcTFxeHjjz/G7du3AZQ3tPPnz0dpaSkSExPx888/11VkIpKITW01dDodwsPDcebMGcTGxqJ58+b4+OOP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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x7f5f16d96cc0>"
      ]
     },
     "execution_count": 3,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "djAlgCircuit.draw(output='mpl')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x7f5f580066d8>"
      ]
     },
     "execution_count": 4,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "plot_histogram(answer)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {},
   "outputs": [],
   "source": [
    "'''\n",
    "CODE OF THE EXAMPLE\n",
    "DESCRIBED ABOVE\n",
    "\n",
    "f(0, 0) = 0 / f(0, 1) = 1 / f(1, 0) = 1 / f(1, 1) = 0\n",
    "\n",
    "balanced function\n",
    "'''\n",
    "\n",
    "size = 2\n",
    "m = 3\n",
    "\n",
    "#Initialize registers\n",
    "qb_rg = QuantumRegister(size+1)\n",
    "b_rg = ClassicalRegister(size)\n",
    "\n",
    "#Build Circuit\n",
    "exDjCircuit = QuantumCircuit(qb_rg, b_rg)\n",
    "\n",
    "#Apply X gate on the last one\n",
    "exDjCircuit.x(qb_rg[size])\n",
    "\n",
    "#Apply H gates\n",
    "exDjCircuit.h(qb_rg)\n",
    "\n",
    "exDjCircuit.barrier()\n",
    "\n",
    "#Apply CNOT gate in function of m\n",
    "for i in range(size):\n",
    "    if (m & (1 << i)):\n",
    "        exDjCircuit.cx(qb_rg[i], qb_rg[size])\n",
    "\n",
    "exDjCircuit.barrier()\n",
    "\n",
    "#Apply H gates\n",
    "for i in range(size):\n",
    "    exDjCircuit.h(qb_rg[i])\n",
    "\n",
    "#Measure the first n qbits\n",
    "for i in range(size):\n",
    "    exDjCircuit.measure(qb_rg[i], b_rg[i])\n",
    "    \n",
    "bk = BasicAer.get_backend('qasm_simulator')\n",
    "atp = 1024\n",
    "res = execute(exDjCircuit, backend=bk, shots=atp).result()\n",
    "ans = res.get_counts()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x7f5f07ff7518>"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "exDjCircuit.draw(output='mpl')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x7f5f07f46780>"
      ]
     },
     "execution_count": 7,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "plot_histogram(ans)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### References"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "__[Wikipedia Deutsch-Jozsa Algorithm](https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm)__\\\n",
    "__[QC - Quantum Computing Series](https://medium.com/@jonathan_hui/qc-quantum-computing-series-10ddd7977abd)__\\\n",
    "__[Learn Quantum Computation using Qiskit](https://community.qiskit.org/textbook/)__"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.6.8"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
