### cvnp: pybind11 casts and transformers between numpy and OpenCV with shared memory


#### Automatic casts:

1. Casts with shared memory between `cv::Mat`, `cv::Matx`, `cv::Vec` and `numpy.ndarray`
2. Casts without shared memory for simple types, between `cv::Size`, `cv::Point`, `cv::Point3` and python `tuple`


#### Explicit transformers between cv::Mat / cv::Matx and numpy.ndarray, with shared memory

````cpp
    pybind11::array mat_to_nparray(const cv::Mat& m);
    cv::Mat         nparray_to_mat(pybind11::array& a);

        template<typename _Tp, int _rows, int _cols>
    pybind11::array matx_to_nparray(const cv::Matx<_Tp, _rows, _cols>& m);
        template<typename _Tp, int _rows, int _cols>
    void            nparray_to_matx(pybind11::array &a, cv::Matx<_Tp, _rows, _cols>& out_matrix);
````


#### Supported matrix types

Since OpenCV supports a subset of numpy types, here is the table of supported types:

````
➜ python
>>> import cvnp
>>> cvnp.print_types_synonyms()
  cv_depth  cv_depth_name np_format  np_format_long
     0         CV_8U         B        np.uint8  
     1         CV_8S         b        np.int8   
     2         CV_16U        H       np.uint16  
     3         CV_16S        h        np.int16  
     4         CV_32S        i        np.int32  
     5         CV_32F        f         float    
     6         CV_64F        d       np.float64
````


### How to use it in your project

1. Add cvnp to your project. For example:

````bash
cd external
git submodule add https://github.com/pthom/cvnp.git
````

2. Link it to your python module:

In your python module CMakeLists, add:

````cmake
add_subdirectory(path/to/cvnp)
target_link_libraries(your_target PRIVATE cvnp)
````

3. (Optional) If you want to import the declared functions in your module:

Write this in your main module code:
````cpp
void pydef_cvnp(pybind11::module& m);

PYBIND11_MODULE(your_module, m)
{
    ....
    ....
    ....
    pydef_cvnp(m);
}
````

You will get two simple functions:
* cvnp.list_types_synonyms()
* cvnp.print_types_synonyms()

````python
>>> import cvnp
>>> import pprint
>>> pprint.pprint(cvnp.list_types_synonyms(), indent=2, width=120)
[ {'cv_depth': 0, 'cv_depth_name': 'CV_8U', 'np_format': 'B', 'np_format_long': 'np.uint8'},
  {'cv_depth': 1, 'cv_depth_name': 'CV_8S', 'np_format': 'b', 'np_format_long': 'np.int8'},
  {'cv_depth': 2, 'cv_depth_name': 'CV_16U', 'np_format': 'H', 'np_format_long': 'np.uint16'},
  {'cv_depth': 3, 'cv_depth_name': 'CV_16S', 'np_format': 'h', 'np_format_long': 'np.int16'},
  {'cv_depth': 4, 'cv_depth_name': 'CV_32S', 'np_format': 'i', 'np_format_long': 'np.int32'},
  {'cv_depth': 5, 'cv_depth_name': 'CV_32F', 'np_format': 'f', 'np_format_long': 'float'},
  {'cv_depth': 6, 'cv_depth_name': 'CV_64F', 'np_format': 'd', 'np_format_long': 'np.float64'}]
````


### Build and test

_These steps are only for development and testing of this package, they are not required in order to use it in a different project._

#### Build

````bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

mkdir build
cd build

# if you do not have a global install of OpenCV and pybind11
conan install .. --build=missing
# if you do have a global install of OpenCV, but not pybind11
conan install ../conanfile_pybind_only.txt --build=missing

cmake ..
make
````

#### Test

In the main repository dir, run:

````
pytest tests
````

#### Deep clean

````
rm -rf build
rm -rf venv
rm -rf .pytest_cache
rm  *.so 
rm *.pyd
````


### Notes

Thanks to Dan Mašek who gave me some inspiration here:
https://stackoverflow.com/questions/60949451/how-to-send-a-cvmat-to-python-over-shared-memory

This code is intended to be integrated into your own pip package. As such, no pip tooling is provided.
