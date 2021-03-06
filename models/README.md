Models
======

For models generated by [SARAH](https://sarah.hepforge.org/) (or by other
means), the SPheno output should be copied into a new directory inside `models/`
that has the same name as your main model.  For example, if the model name is
`SM`, then running `MakeSPheno[]` in Mathematica will create

```
$SARAH_OUTPUT/SM/EWSB/SPheno
```

and the contents of that folder should then be copied into `models/SM`.  The
model-specific SPheno executable and library can now be generated by running the
same steps as above, but adding `-DMODELS=SM` to the `cmake` command.  Multiple
models can be specified by separating them with commas:
`-DMODELS=SM,MSSM,THDM-II`.

As an example, here's the whole procedure for the `SM` model:

```sh
mkdir models/SM
cp $SARAH_OUTPUT/$MODEL/EWSB/SPheno/. models/SM
mkdir build
cd build
cmake -DMODELS=SM
make
make install
```

Note that if your file system supports it, you can create a symbolic link in the
`models/` directory linking to `$SARAH_OUTPUT/$MODEL/EWSB/SPheno`.  This has the
advantage that if you change the model in SARAH, you don't need to copy them
over to SPheno again.  The whole procedure is:

```sh
ln -s $SARAH_OUTPUT/$MODEL/EWSB/SPheno models/$MODEL
mkdir build
cd build
cmake -DMODELS=$MODEL
make
make install
```
