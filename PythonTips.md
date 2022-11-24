# Python and programming tips

A quick collection of sample code that may be useful when working on the KATRIN FoPra.
It is structured in the same order as the tasks.

### 1.1

Define your function depending on only the energy and the two parameters:
```
def differential_spectrum(E, mnu2, E0):
    ...
```

Do not use a python if condition as it does not work with numpy arrays. Instead make use
of [np.heaviside](https://numpy.org/devdocs/reference/generated/numpy.heaviside.html).
For the heaviside condition use `sqrt(mnu2)` if mnu2 is positive. Otherwise set mnu to
zero.

You may want to add a call to
[np.nan_to_num](https://numpy.org/devdocs/reference/generated/numpy.nan_to_num.html)
at the end of the function.


### 1.2

Similar to 1.1, define your transmission function depending on the retarding energy, the
kinetic energy of the electron and the magnetic fields. You can add structure by using
keyword-only arguments.
```
def transmission(qU, E, *, B_ana=6.3E-4, B_max=4.23, B_source=2.52):
    ...
```
Make clever use of heaviside functions to avoid any if-conditions. In case you notice
parts which may emit `nan`, make use of `np.nan_to_num` again.

### 1.3

Combine the functions above into one integrated spectrum that only depends on the
retarding energy and the four fit parameters.

Note that the integrator in scipy cannot deal with arrays as lower limit. You may have
to resort to a python list-comprehension:
```
qU = np.linspace(lower, upper, n_points)
rate = np.array([integrated_spectrum(x) for x in  qU])
```

To search for `f(x) = a`, convert the problem into `f(x) - a = 0` and use a root
searching algorithm. For example
[scipy.optimize.root_scalar](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.root_scalar.html).
Note that you should pass a bracket which constrains the search region, i.e.
```
root_scalar(f, bracket=(lower, upper))
```

### 2.1

To generate random numbers according to a Poisson distribution use
[np.random.poisson](https://numpy.org/devdocs/reference/random/generated/numpy.random.poisson.html).
Note that the size argument is very helpful to generate all the samples you need at once
without a python for loop.

### 2.2

You can use
[scipy.optimize.minimize](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html)
for the numerical minimization. Note that your function can then only depend on the
parameter array:
```
def chi2(params):
    mnu2, E0, A, B = params
    ...
```

It may be helpful to keep in mind that you can define a function anywhere in python,
also in a for loop:
```
for N in datasets:
    def chi2(params):
        ...
    ...
```

### 2.3

This is another problem of the form `f(x) = a` like in 1.3, so the basic idea should be
clear now. Remember that your chi2 now no longer depends on mnu2, only your root
searching function does.
