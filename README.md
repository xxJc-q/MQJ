# MQJ
This is where im learning to code about PDEs and quantum mechanics
## goals
Learn the basics of python for PDEs
Understand the schrodinger equation
Visualize electron probability clouds
# Quantum Physics on a Chromebook!
# Particle in a 1D Box - No installation needed!

Project:
import numpy as np
import matplotlib.pyplot as plt
from scipy.linalg import eigh

print("🚀 Welcome to Quantum Computing on your Chromebook!")
print("Let's calculate quantum wavefunctions...")

# Set up the quantum system
L = 1.0 # Length of the box
N = 1000 # Number of points
x = np.linspace(0, L, N)

# Create and solve the quantum system
dx = x[1] - x[0]
H = np.zeros((N, N))

for i in range(1, N-1):
H[i, i-1] = -1/dx**2
H[i, i] = 2/dx**2
H[i, i+1] = -1/dx**2

H[0, 0] = 1/dx**2
H[-1, -1] = 1/dx**2

# Solve for quantum states
eigenvalues, eigenvectors = eigh(H)

# Show the results
plt.figure(figsize=(10, 6))
for n in range(5):
energy = eigenvalues[n]
wavefunction = eigenvectors[:, n]
wavefunction = wavefunction / np.sqrt(np.trapz(wavefunction**2, x))
plt.plot(x, wavefunction + energy, label=f'State {n+1}, E={energy:.2f}')
plt.axhline(y=energy, color='gray', linestyle='--', alpha=0.3)

plt.xlabel('Position in Box')
plt.ylabel('Energy + Wavefunction')
plt.title('Quantum Particle in a 1D Box - Chromebook Edition!')
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()

print("✅ SUCCESS! You just ran quantum physics on a Chromebook!")
print(f"Ground state energy: {eigenvalues[0]:.4f}")
