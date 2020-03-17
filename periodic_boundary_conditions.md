```python
real scale3 = floor(delta.z*invPeriodicBoxSize.z+0.5f)
delta.xyz -= scale3*periodicBoxVecZ.xyz
real scale2 = floor(delta.y*invPeriodicBoxSize.y+0.5f)
delta.xy -= scale2*periodicBoxVecY.xy
real scale1 = floor(delta.x*invPeriodicBoxSize.x+0.5f)
delta.x -= scale1*periodicBoxVecX.x
```

```python
real scale3 = floor(pos.z*invPeriodicBoxSize.z)
pos.xyz -= scale3*periodicBoxVecZ.xyz
real scale2 = floor(pos.y*invPeriodicBoxSize.y)
pos.xy -= scale2*periodicBoxVecY.xy
real scale1 = floor(pos.x*invPeriodicBoxSize.x)
pos.x -= scale1*periodicBoxVecX.x
```

```python
real scale3 = floor((pos.z-center.z)*invPeriodicBoxSize.z+0.5f)
pos.x -= scale3*periodicBoxVecZ.x
pos.y -= scale3*periodicBoxVecZ.y
pos.z -= scale3*periodicBoxVecZ.z
real scale2 = floor((pos.y-center.y)*invPeriodicBoxSize.y+0.5f)
pos.x -= scale2*periodicBoxVecY.x
pos.y -= scale2*periodicBoxVecY.y
real scale1 = floor((pos.x-center.x)*invPeriodicBoxSize.x+0.5f)
pos.x -= scale1*periodicBoxVecX.x
```