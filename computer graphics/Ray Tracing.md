1. how each object contributes to each pixel?
	1. In object-order rendering:loop each object and create or update the pixel it influenceed.
	2. In image-order rendering:loop each pixel and consider all the object's influence on it.
2. the two contributes ways has different kinds of effect and side effect,but:
3. broadly speaking, image-order rendering is simpler to get working and more flexible in the effects that can be produced, and usually (though not always) takes much more execution time to produce a comparable image
4. Ray tracing is an image-order algorithm for making renderings of 3D scenes
## Basic Ray Tracing Algorithm
1. the basic task for the ray tracing is to find all the object that intersects with the ray that emanates from a pixel in a particular direction.
2. The structure of the basic ray tracing program is:
```
for each pixel do

compute viewing ray

find first object hit by ray and its surface normal n

set pixel color to value computed from hit point, light, and n
```
## Perspective
1. there are many ways of perspective ,eg.cubist painting,fishing lens.liner perspective
2. parallel projection
3. Parallel projections are often used for mechanical and architectural drawings
4. three-point perspective
## intersection
1. how to get the intersection?
	1. For triangle
		1. its answer is to solve a plane's intersection with a line
		2. use the  barycentric coordinate e + td = a + β(b − a) + γ(c − a),form the ternary liner equation,now solve this Ternary equation.
		3. maybe will use the garmer's ruler
	3. For polygon
		1. it may use the normal vector.
		2. (p − p1) · n = 0 the intersection must be perpendicular to the normal verctor
		3. to expand the equation we can solve the t ![[Pasted image 20231130073804.png]]
		4. adding that  we need to determine if p is inside the plane?
		5. how to determine?
		6. The easiest way to do this is to send any 2D ray out from p and to count the number of intersections between that ray and the boundary of the polygon (Sutherland, Sproull,& Schumacker, 1974; Glassner, 1989). If the number of intersections is odd, then the point is inside the polygon; otherwise it is not.(But how practice it i can't compute all the intersection bewteen all the ray from p and the plane...)???
		7. Another approach to polygons, one that is often used in practice, is to replace them by several triangles.
## Shading
1. Lambertian shading model for the point light source
2. its formula： L = kd I max(0, n · l)
3. L is the color in the pixel and the k is the coefficent and the I is dthe intensity of the light source and because of n and l is the unit vetor so n.l equals the cosin sita![[Pasted image 20231130082137.png]]
4. Blinn-Phong Shading 
5. the blinn-phong model think the specular light,how can we measure the light's intensities?
6. its answer is to compare normal vertor and  the half vecrtor the distance between them decide the brightness.
7. ![[Pasted image 20231130210027.png]]![[Pasted image 20231130205954.png]]
8. why add the exponent p?
9. to make it decrease faster
10. Ambient Shading with no dependence on the surface geometry at all.To avoid the dark background
11. Adding the Ambient light to the former formula
![[Pasted image 20231130210505.png]]
12. Multiple Point Lights.for mutiple ligth source the ligth has the property of superposition so we just need add all the effect of these different light.![[Pasted image 20231130210752.png]]

## Shadows
need more time to learn the shadows algorithm
## Ideal Specular Reflection
we need to simulate the situation like this![[Pasted image 20231130220704.png]]
	variant of the Phone light reflection equation (will learn more on the following chapters)
![[Pasted image 20231130220929.png]]
in the real world after the reflection of the power of the light will lose,and the loss of the power is depend of the color.


