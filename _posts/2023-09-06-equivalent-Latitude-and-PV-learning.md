---
title: 'Equivalent latitude:Calculation and usage'
date: 2023-09-06
permalink: posts/2023/09/equivalent-Latitude-and-PV-learning/
tags:
  - climate
  - tools
---
<script async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML"></script>
This is a Learning record of Equivalent latitude.How to calculate it in python/NCL? What are the application scenarios?

### 基础概念理解Concept
Equivalent latitude(等效纬度) is closely link to PV(Potential Vorticity). Usually on isentropic surface(等熵面), pv decreases from the pole to equator in north hemisphere so we can use "equivalent latitude" to mark this trend.
The defination of Equivalent latitude is very simple, most people define it as:
{% raw %}
$$
\phi=\arcsin \left(1-\frac{A}{2 \pi a^2}\right)
$$
{% endraw %}

Where `A` is the area enclosed by specific pv isolines(such as pv=1.0 PVU,then we calculate the area where pv is larger than 1.0 PVU), `a` is the radius of the earth.So the problem becomes how to calculate the area.
In an article[Equivalent Latitude Computation Using Regions of Interest (ROI)](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0072970),they make a comparison between two methods when calculating the area.One is PIECEWISE-CONSTANT,which is widely used,calculates it by sum grids area.The other is ROI.They also provide codes of two methods but unfortunately they are not written in Python.
### An python example code(PIECEWISE-CONSTANT)(North Hemisphere)
```python
import numpy as np
import pandas as pd
def calceqlat(pv, lon, lat, value):
    nlon = len(lon)  # regularly-gridded longitude (degrees)
    nlat = len(lat)  # regularly-gridded latitude (degrees)
    dlat = lat[1] - lat[0]  # latitude grid spacing
    elat2d = np.zeros((nlon, nlat))  # equivalent latitude on 2D grid
    a = 6.37e3  # radius of earth in km
    hemarea = 2. * np.pi * a**2  # surface area of hemisphere in km^2
    latarea = np.zeros(nlat)  # area of grid point at each latitude

    # Calculate area for each grid point
    if max(lat) == 90.:
        # Grid points on the poles
        phi1 = np.pi / 180. * (90.)
        phi2 = np.pi / 180. * (90. - dlat / 2.)
        latarea[0] = hemarea * abs(np.sin(phi1) - np.sin(phi2)) / float(nlon)
        for i in range(1, nlat-1):
            phi1 = np.pi / 180. * (lat[i] + dlat / 2.)
            phi2 = np.pi / 180. * (lat[i] - dlat / 2.)
            latarea[i] = hemarea * abs(np.sin(phi1) - np.sin(phi2)) / float(nlon)
        phi1 = np.pi / 180. * (0. + dlat / 2.)
        phi2 = np.pi / 180. * (0.)
        latarea[nlat-1] = hemarea * abs(np.sin(phi1) - np.sin(phi2)) / float(nlon)
    else:
        # Grid points 1/2 grid off the poles
        for i in range(nlat):
            phi1 = np.pi / 180. * (lat[i] - dlat / 2.)
            phi2 = np.pi / 180. * (lat[i] + dlat / 2.)
            latarea[i] = hemarea * abs(np.sin(phi1) - np.sin(phi2)) / float(nlon)

    totalarea = np.sum(latarea) * nlon
    # Order the data points by increasing value
    npoints = int(nlon) * int(nlat)
    pv1d = np.zeros(npoints, dtype=float)   # Data placed in 1-D array
    a1d = np.zeros(npoints, dtype=float)  # Area placed in 1-D array
    el1d = np.zeros(npoints, dtype=float) # Elat placed in 1-D array
    index = 0
    for ilon in range(0,nlon):
        for ilat in range(0,nlat):
            pv1d[index] = pv[ilat,ilon]
            a1d[index] = latarea[ilat]
            index += 1

    pv_area = pd.DataFrame({'PV':pv1d,'area':a1d})
    A_area = pv_area.loc[pv_area['PV']>=value, 'area'].sum()
    eq_lat = np.degrees(np.arcsin(1-A_area / hemarea))
    return eq_lat
```
#### What is the difference between baroclinic and barotropic?
As adjectives the difference between baroclinic and barotropicis that baroclinic is describing an atmospheric system in which the isobars are at an angle to the isopycnals or isotherms while barotropic is in which the pressure of the atmosphere is dependent upon its density only.
[difference](https://wikidiff.com/baroclinic/barotropic)

### 延展阅读
[transfer hPa to K,isentropic_interpolation](https://unidata.github.io/MetPy/latest/api/generated/metpy.calc.isentropic_interpolation.html)<br>
[Calculate equivalent latitude](https://www.bodekerscientific.com/other/useful-papers-reports-tools/calculate-equivalent-latitude)<br>
[NCL:equivalent latitudes](https://www.ncl.ucar.edu/Applications/equiv_lat.shtml#:~:text=NCL)<br>
[MetPy:PV_baroclinic](https://unidata.github.io/MetPy/latest/api/generated/metpy.calc.potential_vorticity_baroclinic.html)<br>
[MetPy:PV_barotropic](https://unidata.github.io/MetPy/latest/api/generated/metpy.calc.potential_vorticity_barotropic.html)<br>
[“等熵思维”到“等熵位涡思维”回顾与讨论, 周小刚、王秀明等, 2014](http://qxqk.nmc.cn/html/2014/5/20140501.html)<br>
[位温、等熵位涡与锋和对流层顶的分析方法, 陶祖钰等, 2012](http://qxqk.nmc.cn/html/2012/1/20120102.html)<br>