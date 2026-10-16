# json-outputs-from-edm4hep

JSON outputs from all EDM4HEP releases, generated from [FCC Tutorials](https://hep-fcc.github.io/fcc-tutorials/main/2-gen-and-fastsim/2-2-fastsim-delphes/README.html):

## Table of EDM4HEP releases and their respective schema

| Release        | edm4hep | schema |
| -------------- | ------- | ------ |
| **2023-11-23** | 0.10.2  | 1      |
| **2024-03-10** | 0.10.5  | 1      |
| **2024-04-12** | 0.10.5  | 1      |
| **2024-10-03** | 0.99.1  | 2      |
| **2024-10-28** | 0.99.1  | 2      |
| **2024-11-28** | 0.99.1  | 2      |
| **2025-01-28** | 0.99.1  | 2      |
| **2025-05-29** | 0.99.2  | 3      |
| **2026-02-01** | 1.0.0   | 6      |
| **2026-04-08** | 1.0.0   | 6      |

### To know which release corresponds to each schema:

1. Check releases with `source /cvmfs/sw.hsf.org/key4hep/setup.sh --list-releases`
2. Set up the stack with that date `source /cvmfs/sw.hsf.org/key4hep/setup.sh -r YYYY-MM-DD`
3. Convert an event following the tutorial
4. Check the edm4hep version and schema

You can also approximate the dates by looking at https://github.com/key4hep/EDM4hep/releases.

### To summarize

- Schema 2 >= 0.99.0
- Schema 3 == 0.99.2
- Schema 4 >= 0.99.3

## List of collection types and their respective names

**edm4hep::CalorimeterHitCollection**

- CalorimeterHits.

**edm4hep::SimCalorimeterHitCollection**

- ECalBarrelCollection
- ECalEndcapCollection
- HCalBarrelCollection
- HCalEndcapCollection
- HCalRingCollection
- LumiCalCollection
- YokeBarrelCollection
- YokeEndcapCollection

**edm4hep::ClusterCollection**

- EFlowNeutralHadron
- EFlowPhoton
- EFlowNeutralHadron.

**edm4hep::ReconstructedParticleCollection**

- Electron
- Jet
- Muon
- Photon
- ReconstructedParticles

**edm4hep::TrackerHit3DCollection**

- TrackerHits

**edm4hep::SimTrackerHitCollection**

- InnerTrackerBarrelCollection
- InnerTrackerEndcapCollection
- OuterTrackerBarrelCollection
- OuterTrackerEndcapCollection
- VertexBarrelCollection
- VertexEndcapCollection

**edm4hep::TrackCollection**

- EFlowTrack

**edm4hep::VertexCollection**

- BuildUpVertices
- BuildUpVertices_V0
- PandoraStartVertices
- PrimaryVertices
- RefinedVertexJets_vtx
- RefinedVertices

### Source stack

1.0. Setup a FCC software stack release

```bash
# source /cvmfs/sw.hsf.org/key4hep/setup.sh -r 2023-11-23
# source /cvmfs/sw.hsf.org/key4hep/setup.sh -r 2024-03-10
# source /cvmfs/sw.hsf.org/key4hep/setup.sh -r 2024-04-12
# source /cvmfs/sw.hsf.org/key4hep/setup.sh -r 2024-10-03
# source /cvmfs/sw.hsf.org/key4hep/setup.sh -r 2024-10-28
# source /cvmfs/sw.hsf.org/key4hep/setup.sh -r 2024-11-28
# source /cvmfs/sw.hsf.org/key4hep/setup.sh -r 2025-01-28
# source /cvmfs/sw.hsf.org/key4hep/setup.sh -r 2025-05-29
source /cvmfs/sw.hsf.org/key4hep/setup.sh -r 2026-02-01
```

### Event simulation

From [FCC: Getting started with simulating events in Delphes](https://hep-fcc.github.io/fcc-tutorials/main/2-gen-and-fastsim/2-2-fastsim-delphes/README.html).

2.1 Download official pythia cards for the various processes

```bash
# e+ e- -> ZH -> Z and H to anything
wget https://raw.githubusercontent.com/HEP-FCC/FCC-config/main/FCCee/Generator/Pythia8/p8_ee_ZH_ecm240.cmd

# e+ e- -> ZZ -> Z to anything
wget https://raw.githubusercontent.com/HEP-FCC/FCC-config/main/FCCee/Generator/Pythia8/p8_ee_ZZ_ecm240.cmd

# e+ e- -> WW -> W to anything
wget https://raw.githubusercontent.com/HEP-FCC/FCC-config/main/FCCee/Generator/Pythia8/p8_ee_WW_ecm240.cmd

wget https://raw.githubusercontent.com/HEP-FCC/FCC-config/main/FCCee/Delphes/card_IDEA.tcl
wget https://raw.githubusercontent.com/HEP-FCC/FCC-config/main/FCCee/Delphes/edm4hep_IDEA.tcl
```

2.2. Simulate events with DelphesEDM4Hep

```bash
DelphesPythia8_EDM4HEP card_IDEA.tcl edm4hep_IDEA.tcl p8_ee_ZH_ecm240.cmd p8_ee_ZH_ecm240_edm4hep.root
DelphesPythia8_EDM4HEP card_IDEA.tcl edm4hep_IDEA.tcl p8_ee_ZZ_ecm240.cmd p8_ee_ZZ_ecm240_edm4hep.root
DelphesPythia8_EDM4HEP card_IDEA.tcl edm4hep_IDEA.tcl p8_ee_WW_ecm240.cmd p8_ee_WW_ecm240_edm4hep.root
```

2.3. Convert the first 20 events from root files to json

```bash
edm4hep2json -n 20 p8_ee_ZH_ecm240_edm4hep.root
edm4hep2json -n 20 p8_ee_ZZ_ecm240_edm4hep.root
edm4hep2json -n 20 p8_ee_WW_ecm240_edm4hep.root
```

### Full simulation

From [FCC-ee Full Sim General Overview](https://hep-fcc.github.io/fcc-tutorials/main/full-detector-simulations/FCCeeGeneralOverview/FCCeeGeneralOverview.html#towards-full-sim-physics-analyses-with-cld).

3.1. Clone repo

```bash
git clone https://github.com/key4hep/CLDConfig.git
cd CLDConfig/CLDConfig
```

3.2  Retrieve Z(mumu)H(X) MC generator events

```bash
# e⁺e⁻ → Z(μμ)H(inclusive)

wget https://fccsw.web.cern.ch/fccsw/tutorials/MIT2024/wzp6_ee_mumuH_ecm240_GEN.stdhep.gz
gunzip wzp6_ee_mumuH_ecm240_GEN.stdhep.gz
```

3.3. Run the Geant4 simulation (1 event)

```bash
ddsim -I wzp6_ee_mumuH_ecm240_GEN.stdhep -N 1 -O wzp6_ee_mumuH_ecm240_CLD_SIM.root --compactFile $K4GEO/FCCee/CLD/compact/CLD_o2_v07/CLD_o2_v07.xml --steeringFile cld_steer.py
```

3.4 Running CLD reconstruction (1 event)

```bash
sed -i "s/DEBUG/INFO/" CLDReconstruction.py
k4run CLDReconstruction.py --inputFiles wzp6_ee_mumuH_ecm240_CLD_SIM.root --outputBasename wzp6_ee_mumuH_ecm240_CLD --num-events -1

```

3.5. Convert from root files to json

```bash
edm4hep2json wzp6_ee_mumuH_ecm240_CLD_REC.edm4hep.root
edm4hep2json wzp6_ee_mumuH_ecm240_CLD_SIM.root
```

### Types

edm4hep::Vertex:
edm4hep::Track:
edm4hep::TrackerHit:
edm4hep::TrackerHit3D:
edm4hep::TrackerHitPlane:
edm4hep::SenseWireHit
edm4hep::SimTrackerHit:
edm4hep::CalorimeterHit:
edm4hep::SimCalorimeterHit:
edm4hep::Cluster:
edm4hep::ReconstructedParticle:
