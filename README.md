# Maxima plugins

These are very old plugins for [Maxima](http://maxima.sourceforge.net).  I used Maxima a lot when I was an undergrad and grad student, and I hope to be able to get back into mathematics in detail sometime, in which case I will likely use this again.

I made these plugins a very long time ago, before I knew anything at all about pragmatic programming.  Documentation isn't very good and indentation is pretty much nonexistent.

I made a [tutorial series on YouTube](https://www.youtube.com/playlist?list=PLEDEE2F7C6750729F) for Maxima, and in it I shared a link to my own personal webpage to these plugins.  I decided it was time to update the links to use GitHub instead.

Below is the original documentation.

matrixtools.mac
---------------

Matrixtools has several functions for matrices, and some for lists.  See the file itself for detailed comments, but briefly:
* First and foremost a pivor function.  pivot(M,j,k) will pivot matrix M around the entry in the *j*th row and the *k*th column.
* A reduced-row echelon form function, redech(M).  This is different from the one that you'll find if you google "rref maxima"; I actually created this one myself from native Maxima functions.  redech(m) will return the reduced row echelon form of the matrix M.
    * You can also use rref(M), which calls redech(M), in case you have an emotional attachment to TI-Basic.
    * The rref plugin that you find through google is actually incompatible with this plugin, so don't load them both at once!
* A row scalar multiplication function.  rowmult(M,i,n) will multiple the *i*th row of matrix M by *n*.
    * mrow(M,i,n) calls rowmult(M,i,n), again, if you have an emotional attachment to TI-Basic.
* matd(j,k,L) will create a *j* by *k* matrix using the entries from list L.  It's easier (I think) than Maxima's regular matrix() command.
* randommatrix(a,b) will return a matrix of dimensions *a*,*b* with pseudorandom entries, which will be integers between -100 and +100.
* rowdim(M) and coldim(M) return the row dimension and the column dimension of the matrix M, respectively.
* sumlist(L) will sum up all of the entries in list L.  It's actually very simple and I've created this on-the-fly several times, but finally decided to put it into a plugin.
* inlist(x,L) will return "true" if *x* is an element in list *L*.  Note, though, that if *L* has lists as elements, inlist will not check to see if *x* is in those particular lists. i.e., if L=[[1,2,3],[4,5]], inlist(2,L) will return "false," but if L=[1,2,3,4,5], inlist(2,L) will return "true."
* appendmatrix(M,N) will take two matrices, *M* and *N*, and put them sie-by-side in a new matrix (assuming that rowdim(M)=rowdim(N)).  The regular "append" command will put two matrices on top of each other, which isn't what we usually mean when we talk about appending matrices.
* invmod(M,a) will find the inverse of matrix *M modulo a*, instead of just the regular inverse of matrix *M*.
* sortA(L) and sortD(L) sory lists in ascending and descending order, and they should work with irrational numbers (the regular sort command only works for rational numbers).  You can also use sorta(L) or sortd(L) if you don't want to bother with capital letters.
* location(L,n) will return a list that gives all of the locations of *n* in the list *L*.
* listsection(L,m,n) makes a list with the *m*th through the *n*th entries of *L*.
* There are other functions in matrixtools.mac, too, but they are either not useful or only exist to be used by other functions.

riemann.mac
-----------

Riemann has exactly one function, riemann(f,x,a,b,n), which is a method of numerically approximating integrals.  It uses the Riemann definition of definite integration (or at least something like it).  Its format is identical to integrate(f,x,a,b), except with the added *n*, which is how many rectangles you want to use in your approximation (higher is better).  For most cases, romberg(f,x,a,b), a native Maxima function, is probably better than riemann and almost certainly faster, but I wanted to have this available for myself because I have run into occasions when romberg fails to converge.

complex.max
-----------

Complex has a few tools for complex-valued functions.  Note that it does not automatically declare *z* to be a complex variable.  So, you might want to enter declare(z,complex); before using conjugate(), realpart(), imagpart(), etc.  Again, see the file itself for more detailed comments.

* Complex line integrals:
    * comlineint(f,zt,a,b) finds the complex line integral of f=f(z) over the complex parameterization zt=z(t).  f(z)mus be in terms of z, and z(t) *must* be in terms of t.  Sorry, I was too lazy to make it more versatile, but if you watch my videos you should be able to figure out how to change it yourself if you so desire.  :D
    * comelineapp(f,zt,a,b) approximates the complex line integral instead of trying to solve it analytically.  This is useful for extraordinarily difficult complex line integrals that Maxima can't solve analytically (and, unless you're integrating a simple, closed curve that lies in a simply-connected region on which f is holomorphic, there's a good chance that Maxima won't be albe to solve it analytically).  Comlineapp uses romberg to approximate these integrals.  Again, f(z) *must* be in terms of z and z(t) *must* be in terms of t.
    * conlinenum(f,zt,a,b,n) also approximates the complex line integral, but it does so using the riemann function from the tiemann.mac file (which is included in the complex.mac file). *n* is the number of rectangles *divided by 1,000*!  So that means if you set *n* as 1, comlinenum uses 1,000 rectangles, which is quite a lot already.
* Approximating residue:
    * resapp(f,p,k) approximates the residue of f(z) at point *p*, and *k* needs to be *half the sidelength of any square such that if p is in the center of that square, there are no singularities in that square other than p*.  Put more simply, make *k* small enough that there are no singularities within sqrt(2\*k) from *p* (but *p* can itself be a singularity).  resapp uses comlineapp to approximate the value of the residue by finding the complex line integral of the box around *p*.  99% of the time, the native Maxima function "residue" will be better (look at the Maxima manual for information on that function), but if you're finding the residue of an essential singularity, resapp will be able to approximate it, whereas "residue" will either fail or give you something that's wrong.
    * resnum(f,p,k,n) also approximates the residue of f(z) at point *p*, and *k* still needs to be half the sidelength of the box surrounding *p*, but it uses comlinenum instead of comlineapp, which means that it uses riemann to apprixmate the integral, but I did let it run all night one time with n=50 (i.e., 50,000 rectangles for both integrals).  It takes forever, I'm not going to lie.  resapp is probably better most often because it uses romberg, but, again, there are times that romberg fails to converge.
* Finding the order of poles and zeros:
    * orderpole(f,p) returns the order of the pole *p* for the function f(z), and it starts by testing to see if there is, in fact, a pole at the point *p*.  (Otherwise, it would be stuck in an infinite reursive loop.)  Success is limited; although it is more oftehn than not useful, I once let it run and was able to find out the order of a pole by hand within about a minute, whereas orderpole was running for a couple of hours before I finally interrupted it.  More often than not, though, this is muhc faster than by hand; that was a sort of unusual expression.
    * orderzero(f,p) returns the order of the zero of f(z) at point *p*.  It also checks to make sure that there is actually a zero before doing it.
    * The command order(f,p) combines the previous two commands to find if it is a pole or zero, then it displays its order.  If the point *p* is neither, it will say so.
* comfactor(expr,x) will factor *expr* (which is a polynomial of exactly one variable, being *x*) into its complex roots (i.e., the fundamental theorem of algebra).  Unlike the previous functions, *expr* can be of *any* variable, but it *must* be defined by the second argument.  Comfactor works by using the "solve" command, so if Maxima has a hard time finding the zeros of the polynomial, it will have a hard time factoring the expression.  This is different from "gfactor" in that it will actually factor into a(x-x1)(x-x2)...(x-xp) where x1...xp are all parts of the roots of the polynomial, not just complex numbers where the real and imaginary parts are integers.
* There are other functions in complex.mac, too, but they're mostly residue functions that were made unnecessary when I discovered that Maxima has a native residue function.

ineqgraph.mac
------------

There aren't any comments in this particular file, in fact, I was pretty lazy when I made it, so you'll see some meangingless wxMaxima code.  This grpahs inequalities, but it does not solve them, so it's for display purposes only.  I created it for [an Algrebra 2 video](http://www.youtube.com/watch?v=6zHRKQFWNg8&list=PLF44614421AC4E67A&index=45&feature=plpp_video) (and [one more](http://youtu.be/Cya1DTnYv2A)).  It's actually very limited, but a viewer asked for it.  It only goes from -10 to 10.
* It requires that you first load the draw package with load(draw);  It does not automatically load the draw package.
* lessthan(a) graphs the inequality x\<a.
* lessthaneq(a) graphs the inequality x\<=a (x is less than or equal to a)
* greaterthan(a) graphs x\>a
* greaterthaneq(a) graphs x\>=a
* between(a,b) graphs a\<x\<b.
* betweeneq(a,b) graphs a\<=x\<=b.
* outside(a,b) graphs x\<a or x\>b.
* outsideeq(a,b) graphs x\<=a or x\>=b.

cryptoaffine.mac
----------------

I created this file when I was in a cryptology class.  I actually only got around to writing a function for the affine transformation cryptosystem. and it's been so long since I"ve used it that I'm going to have a hard time remembering how.  It's probably only of minimal use (and there are muhc better cryptology tools out there; I think that's why I didn't write more than this one).
    * It makes heavy use of lists.  When you input a message to by encrypted or decrypted, you need to make a list, like [h,e,l,l,o,w,o,r,l,d], cryptoaffine automatically assigns these letters to their appropriate numerical value.
    * affine(V,a,b) is the actually affine transformation function.  It multiples each entry in the list V by a, then adds b, and reduces it modulo 26.  It will return a list of numbers, not leters.
    * totext(V) will convert the list of numbers (either created by affine or entered directly by the user) into letters, but to avoid conflicting with the already assigned values for those letters, it puts an underscore after every letter.


inequalities.mac
----------------

This solves inequalities of one variable, but in a fairly inexact way.  It only uses one command (there's two in there, but only one that you'd use): solveineq(expr,x), where expr is the expression and x is the variable (though it will always return the variable as x). Only for use with continue or rational functions (or a mixture of continuous and rational functions); i.e., the only kind of discontinues it can handle is removeables and asymptotes.  Not for use wiht any function that will have an infinite number of intervals that satisfy expr.  There's a lot more detail in the comments in the file.
