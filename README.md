# martini_membrane_simulation (uses martini3 stable version, there is another beta version but that uses another force field and different martinize)
martinize2 -ff martini3001 -f AA.pdb -x CG.pdb -o topol.top -scfix -dssp /usr/bin/dssp -elastic –merge A,B,C​ #if protein is multimeric then better to merge chains
gmx_mpi editconf -f CG.pdb -d 4.0 -bt dodecahedron -o CG.gro​
gmx_mpi grompp -p topol.top -f minimize.mdp -c CG.gro -o minimization-vac.tpr -r CG.gro -maxwarn 1​
gmx_mpi mdrun -deffnm minimization-vac -v
python2 insane.py -f minimization-vac.gro -o system.gro -p system.top -pbc square -box 18,18,23 -l POPG:1 -l POPE:2 -u POPG:1 -u POPE:2 -fudge 0.3 -salt 0.1 -center -dm 3 -sol W -ring​ 
gmx_mpi insert-molecules -f system.gro -ci molecule.gro -nmol 100 -o system_lig.gro​
gmx_mpi grompp -f ions.mdp -c system_lig.gro -p system.top -o system_lig_ions.tpr -maxwarn 1​
gmx_mpi genion -s system_lig_ions.tpr -o system_lig_ions.gro -p system.top -pname NA -nname CL -neutral​
