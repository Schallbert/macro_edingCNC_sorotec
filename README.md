# macro_edingCNC_sorotec
macros for Eding's Computerized Numerical Controllers, specialized for Sorotec portal milling machines 

## Preface / Resources
Please visit my [website](https://schallbert.de/macros-for-cnc/) for an in-depth introduction into CNC macros. 

*"A Macro or subroutine is a collection of instructions written for the CNC interpreter to perform actions. They are often used to automate recurring operations."*

There are many good third party resources out there to learn about writing them, e.g. [cnccookbook](https://www.cnccookbook.com/cnc-macro-programming-fanuc-macro-b/), too.

The macros provided in this repository are somewhat specialized to [EdingCNC](https://edingcnc.com/)'s interpreter and are starting off a 'fork' of the non-version-controlled `Standard_Macro_V2-1e` by [Sorotec](https://www.sorotec.de/).

## Disclaimer
@TODO: Add exclusion of liability clause for any harm or "misbehavior" of the macros. Also add that sole responsibility stays with the user of the macro, and that the user has to make sure that everything works as intended

## Folder structure of this repo
This repository has two folders for assets:
- `dialogPictures` that keeps images presented in the dialog message boxes
- `op_f_key` that keeps icons of 'user' macro's function buttons

The images are pulled by edingCNC based on their file names. For dialog pictures, e.g. the `dlgmsg`'s title parameter has to exactly match the corresponding file name in `dialogPictures`.
Within the `user` folder, icon names have to match the corresponding user macro number, e.g. `U3` for user `sub user_3`.

## Macro routines
@TODO: Explain the routines and its names (some hardcoded) 

# Working
@TODO: Add asset folder for icons and show images of macro execution within EdingCNC.
