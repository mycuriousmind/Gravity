# 🌌 N-Body Gravity Simulator

A comprehensive interactive simulation of gravitational interactions between multiple particles. This project combines a physics engine (NumPy), backend API (FastAPI), database persistence (SQLite), and interactive frontend (Streamlit).

## Features

✨ **Core Functionality:**
- 🎯 User-defined number of bodies (2-100)
- 📊 Randomized masses and dynamic position vectors
- ⚛️ Accurate gravitational force calculations using Newton's law
- 🖱️ Interactive particle selection to analyze forces
- 📈 Real-time visualization of particle dynamics
- ⏸️ Start/Stop/Step controls for simulation

✨ **Advanced Features:**
- 🔄 Auto-run or manual stepping
- 📉 Velocity and kinetic energy analysis
- 💾 Database persistence of simulation states
- 🎨 Multiple visualization modes
- 📊 Particle statistics and properties
- 🚀 Fast numerical integration with NumPy

## Tech Stack

### Frontend
- **Streamlit**: Interactive web UI
- **Plotly**: Advanced data visualization
- **Matplotlib**: Scientific visualization

### Backend
- **FastAPI**: REST API server
- **NumPy**: Numerical computations
- **Uvicorn**: ASGI server

### Database
- **SQLite**: Persistent storage of simulation data
- **JSON**: Data serialization

## Project Structure

```
Gravity/
├── physics.py          # Core N-body physics simulation engine
├── database.py         # SQLite database operations
├── main.py             # FastAPI backend server
├── app.py              # Streamlit frontend application
├── requirements.txt    # Python dependencies
├── gravity_sim.db      # SQLite database (auto-created)
└── README.md           # This file
```

## Installation

### Prerequisites
- Python 3.8 or higher
- pip (Python package manager)

### Setup

1. **Clone/Navigate to project:**
   ```bash
   cd c:\Github\Python\Gravity
   ```

2. **Create virtual environment (optional but recommended):**
   ```bash
   python -m venv venv
   # On Windows:
   venv\Scripts\activate
   # On macOS/Linux:
   source venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

## Running the Application

The application has two components that must run simultaneously:

### Terminal 1: Start FastAPI Backend Server

```bash
python main.py
```

Or using uvicorn directly:
```bash
uvicorn main:app --reload --port 8000
```

The API will be available at: `http://localhost:8000`
- API documentation: `http://localhost:8000/docs` (Swagger UI)
- Alternative docs: `http://localhost:8000/redoc` (ReDoc)

### Terminal 2: Start Streamlit Frontend

```bash
streamlit run app.py
```

The Streamlit app will open at: `http://localhost:8501`

## Usage Guide

### Creating a Simulation

1. Open Streamlit UI (http://localhost:8501)
2. In the sidebar, set the desired number of bodies (2-100)
3. Click **"🚀 Create Simulation"** button
4. A new simulation is initialized with:
   - Random masses (1-100 kg)
   - Random initial positions in a 100×100 box
   - Small random velocities

### Running the Simulation

**Manual Mode:**
- Click **"⏯️ Step 1"** to advance one time step
- Click **"⏭️ Step 10"** to advance 10 time steps
- Click **"⏭️⏭️ Step 50"** to advance 50 time steps

**Auto-Run Mode:**
- Enable **"🔄 Auto-run"** checkbox in sidebar
- Adjust **"Steps per refresh"** slider (1-100) to control speed
- The simulation will continuously advance

### Analyzing Forces

1. In the **"Select Particle"** section, choose a particle ID
2. Click **"🎯 Select Particle"**
3. The right panel shows:
   - Force magnitude (|F|) in Newtons
   - Direction angle in degrees
   - Acceleration (a = F/m)
   - Force components (Fx, Fy)
   - Visual force diagram

### Viewing Analytics

Three analysis tabs are available:

- **Kinetic Energy**: Particle positions colored by kinetic energy
- **Velocity Distribution**: Histogram of particle speeds
- **Particle Stats**: Table of all particle properties

### Controlling the Simulation

- **Reset**: Return to initial conditions
- **Delete**: Clear this simulation and start fresh

## Physics Explanation

### Gravitational Force

The gravitational force between two particles is calculated using Newton's law of universal gravitation:

$$F = G \frac{m_1 m_2}{r^2}$$

Where:
- **G** = gravitational constant (6.674 × 10⁻¹¹ N·m²/kg²)
- **m₁, m₂** = masses of the two particles
- **r** = distance between particles

### Force Direction

The force on particle i due to particle j is directed along the line connecting them:

$$\vec{F}_i = \frac{G m_i m_j}{r^2} \hat{r}$$

Where $\hat{r}$ is the unit vector from i to j.

### Numerical Integration

Position and velocity are updated using the Euler method:

$$v_{n+1} = v_n + a_n \cdot \Delta t$$
$$x_{n+1} = x_n + v_n \cdot \Delta t$$

Where:
- **a** = acceleration (F/m)
- **Δt** = time step (0.01 seconds)

### Boundary Conditions

Particles wrap around periodic boundaries (toroidal topology) to simulate an infinite space.

## API Endpoints

All endpoints return JSON responses. Base URL: `http://localhost:8000`

### Create Simulation
```
POST /simulation/create
Body: {
  "num_bodies": 10,
  "box_size": 100.0,
  "time_step": 0.01,
  "g_constant": 6.674e-11
}
```

### Get Simulation State
```
GET /simulation/{sim_id}/state
```

### Advance Simulation
```
POST /simulation/{sim_id}/step          # Single step
POST /simulation/{sim_id}/steps?num_steps=10  # Multiple steps
```

### Get Force on Particle
```
GET /simulation/{sim_id}/particle/{particle_idx}/force
```

### Get Particle Information
```
GET /simulation/{sim_id}/particle/{particle_idx}
GET /simulation/{sim_id}/all-particles
```

### Reset Simulation
```
POST /simulation/{sim_id}/reset
```

### Delete Simulation
```
DELETE /simulation/{sim_id}
```

## Database Schema

### simulations
- `id`: Simulation ID
- `num_bodies`: Number of particles
- `created_at`: Creation timestamp
- `duration`: Total simulation duration
- `box_size`: Size of simulation box
- `time_step`: Integration time step
- `g_constant`: Gravitational constant

### states
- `id`: State record ID
- `simulation_id`: FK to simulations
- `step`: Time step number
- `time`: Simulation time
- `positions_json`: JSON array of positions
- `velocities_json`: JSON array of velocities
- `masses_json`: JSON array of masses

### forces
- `id`: Force record ID
- `simulation_id`: FK to simulations
- `step`: Time step number
- `particle_idx`: Particle index
- `force_x`, `force_y`: Force components
- `force_magnitude`: Magnitude of force

## Configuration

Key parameters in `physics.py`:

```python
NBodySimulation(
    num_bodies=10,           # Number of particles
    G=6.674e-11,             # Gravitational constant
    box_size=100.0,          # Size of simulation box
    time_step=0.01           # Integration time step (seconds)
)
```

Adjust these values in `app.py` to change simulation behavior.

## Performance Notes

- **Update Speed**: The simulation involves O(N²) force calculations
- **Recommended**: 10-50 particles for smooth, real-time simulation
- **Maximum**: 100+ particles possible, but may run slower
- **Database**: States are saved every 10 steps to minimize I/O

## Troubleshooting

### API Connection Error
- Ensure FastAPI server is running: `python main.py`
- Check that port 8000 is available
- Streamlit should connect automatically if the backend is running

### Slow Performance
- Reduce the number of bodies or increase the step size
- Reduce refresh rate in Streamlit UI
- Close other applications consuming resources

### Database Issues
- Delete `gravity_sim.db` to reset the database
- Check file permissions in the project directory

## Future Enhancements

- 🎥 Trajectory tracking and visualization
- 💾 Save/load simulation states
- 🎨 Customizable particle properties
- ⚡ GPU acceleration with CuPy
- 🌐 Multi-user sessions with WebSockets
- 🎮 Joystick/game controller support

## License

This project is provided as-is for educational and research purposes.

## References

- Newton's Laws of Motion and Gravity
- Numerical Methods for ODE Integration
- N-Body Simulation Techniques
- FastAPI Documentation: https://fastapi.tiangolo.com
- Streamlit Documentation: https://docs.streamlit.io

---

**Enjoy exploring gravitational dynamics! 🌌**
