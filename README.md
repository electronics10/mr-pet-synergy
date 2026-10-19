# mr-pet-synergy

Year 2 project (2026-08 to 2027-07) of NSTC grant 114WFAB910936 (PI 陳志成), BIPI lab.
Status: **draft README, 2026-10-19**. Everything under "Decided" was confirmed by the project lead;
everything else is open.

## Goal

Find out whether, and by how much, an MR image of the same animal helps produce a better PET image.

We compare two kinds of deep-learning methods that use MR:

- **Image-based networks:** PET image + MR image in, better PET image out. The architecture is not
  fixed (e.g. U-Net, diffusion model).
- **Unrolled network:** a few reconstruction iterations with a small learned network inside each one.
  More explainable than an image-based network.

Which kind works better is the research question; we don't know yet. Classical MAP-EM reconstruction,
with and without MR guidance, is the baseline.

The grant's Year 2 aims (from the grant proposal, which is not public) are broad:

> 2.1 Implement AUTOMAP and U-net architectures with multi-channel input for synergistic PET/MRI data
> reconstruction, enhancing quantitative accuracy and image quality.
>
> 2.2 Develop CNN based models to integrate and analyze PET and MRI data synergistically, extracting
> complementary information for quantitative metrics.

The PI allows freedom as long as the work stays close to these. What matters is good results for next
year's funding.

## Decided

- **No AUTOMAP**, even though the proposal names it.
- **The MAP-EM baseline lives in the shared toolkit** ([pet-sim-recon](https://github.com/electronics10/pet-sim-recon)), not here. It includes a
  PET-only penalty and an MR-guided one (e.g. Bowsher weights). A working prototype of both exists.
- **Simulated PET** comes from MCGPU-PET through the toolkit.
- **The unrolled network needs raw PET data** (sinograms). The real scanner doesn't give those, so for
  now the unrolled network runs on simulated data only; this changes if real raw data become available.
  Image-based networks can also run on the real scanner's images.
- **Simulated MR is a formula-based approximation** computed from the object, not a physical
  simulation. This is a known weakness of the study.
- **Simulated objects:** many random shapes (each object 1–6 ellipsoids or elliptic cylinders with
  random size, position, density and activity), so models learn something general. Realistic digital
  mice may be added.
- **Real data:** images reconstructed by the scanner's own software, of physical phantoms scanned on the
  Bruker 7 T PET/MRI. First session: 2026-10-20.
- **Scan data are never committed** to git.

## Side checks (worth reporting only if the effect turns out large)

- **Lesions MR can't see:** a PET hot spot with no matching MR edge. Does MR guidance blur it away?
- **Dose level:** we work at low dose, where MR should help most. Check whether the gain disappears at
  high dose. Assumed, not verified.

## Later (only after everything else)

- **PET and MR both as outputs:** an optional wish from the PI. Images in, images out is acceptable.

## Open questions

1. How to judge "a better PET image": error against the true image, correct region values, how well
   small lesions keep their contrast, or a mix.
2. The 2026-10-20 scan: plan under discussion, tracked in an issue. Preferred so far: one filling per
   phantom, count levels from frame length; check once that a short frame at high activity matches a
   long frame at low activity.
3. What the real scans are for, given the unrolled network can't use them: testing image-based networks on real
   images, checking how realistic the simulator is, or both.
4. How random shapes get MR values. Agreed in principle, still to verify and revise: each shape gets its
   own MR value, chosen independently of its PET activity, so lesions MR can't see come for free.
5. Which realistic mouse models to use, if any (Digimouse is free; MOBY needs a licence).
6. Which dose levels to simulate.

## Previous works (may serve as prototypes)

- [mcgpu-recon](https://github.com/electronics10/mcgpu-recon) (MAP-EM with uniform, Bowsher and RDP penalties)
