.. _tudatFeaturesInputOutput:

Input/Output
============
Handling input and output can be an important part of an application, as Tudat is built primarily for numerical orbit propagation/mission analysis, and not for visualization, which most users choose to do in e.g. Matlab. This page provides an overview of the most important Tudat methods for handling input and output, their set-up and how they can be used in an application.

Available methods
~~~~~~~~~~~~~~~~~
All functions and classes that handle input/output can be found in the :literal:`InputOutput` directory in the Tudat libraries. These functions and classes provide a variety of handlers and can, for example, skip lines, eliminate comments from a file, or store the propagation history to a particular file. In most cases, however, these specific file readers will not need to be called directly by the user, but are instead used when retrieving a default spherical harmonic gravity field or an aerodynamic database.

The input/output elements from Tudat that will be discussed here, can be subdivided into three categories:

- Path-functions.
- File-readers.
- File-writers.

Path-functions
**************
These are functions that return the root-path of the library (or a particular folder). They can be used to define a path relative to the root-path of Tudat when using a file-reader/writer. The following path functions are available:

:literal:`getTudatRootPath()` (Tudat)
    This function returns the root-path corresponding with the root-directory of the Tudat library as a string with trailing slash included.

:literal:`getSpiceKernelPath()` (Tudat)
    This function returns the path where the Spice Kernels are located as a string with trailing slash included. This is very useful when writing code that will work cross-platform. For instance, if you have a file inputData.txt that is located at::

        C:/Software/tudatBundle/tudat/Tudat/Astrodynamics/
    
    you will want to avoid writing this full path in your code, as it will not run on any other system where Tudat is installed in a different directory. Instead, what you can do:

    .. code-block:: cpp

        std::string filePath = input_output::getTudatRootPath( ) + "Astrodynamics/";
        std::string fileName = "inputData.txt" 

    .. note:: The full path/name of the input data file is now simply filePath+fileName, which will correctly identify the file location on any system.

File-readers
************
If neeeded, you can use this functionality as a starting point to create your own file-reader for your specific file type. If you run into issues when doing so, please contact the Tudat support team. However, there is a dedicated file-reader available in the Tudat library:

:literal:`readMatrixFromFile` (Tudat)
    This function can be used to read a simple text file with separated numbers. The documentation includes the options for a separator and the character used to skip a line (e.g. a comment-line). Note that the new-line character (\n) is reserved to split the lines of the matrix.

File-writers
************
The :literal:`InputOutput` directory in the Tudat library also contains functionality to write data (e.g. the propagation history of a satellite) to a file. The following function is available:

:literal:`writeDataMapToTextFile` (Tudat)
    This function writes data stored in a map to a text file. A number of overloads exists for this function based on the input given to the function. Furthermore, the data-map can store different types of data (e.g. doubles and Eigen vectors, which are typical types for the propagation history).

    The simplest input consists of the data-map and the file-name, the function writes data stored in the map to text file, using default KeyType-precision and ValueType-precision (digits10 from "limits" standard library), output directory (Tudat root-path), and delimiter (space).

Examples
~~~~~~~~
Data-map (double,double) to text file
*************************************
A data map is a template class that is defined by its key-type and value-type:

.. code-block:: cpp

    std::map< key-type, value-type >

Using this, a data map, where the type of the key is a :literal:`double`, and the type of the value is also a :literal:`double`, can be defined as:

.. code-block:: cpp

    std::map< double, double > keyDoubleValueDoubleMap;

Each entry in the data map consists of a key and a value and is entered using:

.. code-block:: cpp

    keyDoubleValueDoubleMap[ key ] = value;

As an example, three entries are stored in this data map:

.. code-block:: cpp

    keyDoubleValueDoubleMap[ std::sqrt( 3.0 ) ] = 1.0 / std::sqrt( 2.0 );
    keyDoubleValueDoubleMap[ 4.5 ] = 56.89;
    keyDoubleValueDoubleMap[ 12.65 ] = 1.0 / 3.0;

Now, this data-map can be stored to a file using:

.. code-block:: cpp

    tudat::input_output::writeDataMapToTextFile(
                keyDoubleValueDoubleMap, "keyDoubleValueDoubleMapDataFileWithDefaults" );

Data-map (double,Vector3d) to text file
***************************************
An example of a data map, where the type of the key is a :literal:`double`, and the type of the value is an :literal:`Eigen::Vector3d`:

.. code-block:: cpp

    std::map< double, Eigen::Vector3d > keyDoubleValueVector3dMap;
    keyDoubleValueVector3dMap[ 1.1 ] = Eigen::Vector3d( 0.0, 1.3, -6.54 );
    keyDoubleValueVector3dMap[ 6.5 ] = Eigen::Vector3d( -4.56, 1.23, -9.98 );
    keyDoubleValueVector3dMap[ 10.9 ] = Eigen::Vector3d( -46.13, 1.0 / 3.0, std::sqrt( 2.0 ) );

This data-map can be stored to a file using:

.. code-block:: cpp

    tudat::input_output::writeDataMapToTextFile(
                keyDoubleValueVector3dMap, "keyDoubleValueVector3dMapDataFile" );

Storing propagation history
***************************
A good example on how to store the propagation history in a data map can be found in the example applications in the Tudat Bundle. If you have downloaded the bundle, these examples can be found in::

    tudatBundle/tudatApplications/satellitePropagatorExamples/SatellitePropagatorExamples

The applications in both :literal:`asterixAndObelixPropagator.cpp` and :literal:`galileoConstellationSimulator.cpp` contain examples on how to store the propagation history to a data file.
