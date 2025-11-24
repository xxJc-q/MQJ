# MQJ
just trying out stuff



# Plot gravitational and total time dilation vs radius for Earth-like mass.
import math
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

# Constants
G = 6.67430e-11      # m^3 / kg / s^2
c = 299792458        # m / s
M_earth = 5.972e24   # kg
R_earth = 6.371e6    # m

def grav_factor(M, r):
    rs = 2 * G * M / c**2
    # protect against r <= rs
    if r <= rs:
        return 0.0
    return math.sqrt(1 - rs / r)

def orbital_velocity(M, r):
    # circular orbit velocity (Newtonian approximation) v = sqrt(GM/r)
    return math.sqrt(G * M / r)

def total_factor(M, r):
    # combine gravitational and special relativistic time dilation for an object orbiting at velocity v
    v = orbital_velocity(M, r)
    special = math.sqrt(1 - (v**2 / c**2))
    grav = grav_factor(M, r)
    return grav * special, grav, special, v

# Radii range from just above Earth's surface to 50,000 km altitude
radii = np.linspace(R_earth * 1.001, R_earth + 5e7, 400)  # meters

results = [total_factor(M_earth, r) for r in radii]
total = np.array([r[0] for r in results])
grav = np.array([r[1] for r in results])
spec = np.array([r[2] for r in results])
vels = np.array([r[3] for r in results])

# Prepare a dataframe for display
df = pd.DataFrame({
    "radius_m": radii,
    "altitude_km": (radii - R_earth) / 1000.0,
    "time_factor_total": total,
    "time_factor_gravitational": grav,
    "time_factor_special": spec,
    "orbital_velocity_m_s": vels
})

# Show a small sample of the dataframe
import caas_jupyter_tools as jt; jt.display_dataframe_to_user("Time dilation vs radius (sample)", df.head(12))

# Plot 1: gravitational and total time dilation vs altitude
plt.figure(figsize=(8,5))
plt.plot(df["altitude_km"], df["time_factor_gravitational"], label="Gravitational factor (dτ/dt)")
plt.plot(df["altitude_km"], df["time_factor_total"], label="Total factor (orbiting)")
plt.xlabel("Altitude (km)")
plt.ylabel("Time dilation factor (dτ/dt)")
plt.title("Time dilation vs altitude for circular orbits around Earth")
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()

# Plot 2: orbital velocity vs altitude
plt.figure(figsize=(8,5))
plt.plot(df["altitude_km"], df["orbital_velocity_m_s"])
plt.xlabel("Altitude (km)")
plt.ylabel("Orbital velocity (m/s)")
plt.title("Circular orbital velocity vs altitude")
plt.grid(True)
plt.tight_layout()
plt.show()

# Print example values for surface and GPS altitude (~20,200 km)
gps_alt = R_earth + 20200e3
surface_vals = total_factor(M_earth, R_earth)
gps_vals = total_factor(M_earth, gps_alt)

print(f"Earth surface gravitational factor: {grav_factor(M_earth, R_earth):.12f}")
print(f"Earth surface total factor (if orbiting at surface speed): {surface_vals[0]:.12f}")
print("")
print(f"GPS altitude (20200 km) gravitational factor: {gps_vals[1]:.12f}")
print(f"GPS altitude (20200 km) special-relativistic factor: {gps_vals[2]:.12f}")
print(f"GPS altitude (20200 km) combined total factor: {gps_vals[0]:.12f}")

