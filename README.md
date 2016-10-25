# Kerasify

Kerasify is small library for running Keras models from a C++ application. 

Design goals:

* Compatibility with image processing Sequential networks generated by Keras using Theano backend.
* No external dependencies, standard library, C++11 features OK.
* Model stored on disk in binary format that can be quickly read.
* Model stored in memory in contiguous block for better cache performance.
* Unit testable, rigorous unit tests.

Looking for more Keras/C++ libraries? Check out https://github.com/pplonski/keras2cpp/

# Example

From Python:

```
test_x = np.random.rand(10, 10).astype('f')
test_y = np.random.rand(10).astype('f')

model = Sequential()
model.add(Dense(1, input_dim=10))

model.compile(loss='mean_squared_error', optimizer='adamax')
model.fit(test_x, test_y, nb_epoch=1, verbose=False)

from kerasify import export_model
export_model(model, 'example.model')
```

From C++:

```
// Initialize model.
KerasModel model;
bool result = model.LoadModel("example.model");

// Run prediction.
Tensor in(1), out;
in.data_ = {{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}};

result = model.Apply(&in, &out)
```

Add `keras_model.cc` to your build and you should be good to go.

# Unit tests

To run the unit tests, generate the unit test models and then run `keras_model_test`:

```
$ python make_tests.py
...

$ make
cppcheck --error-exitcode=1 keras_model.cc
Checking keras_model.cc...
Checking keras_model.cc: DEBUG...
g++ --std=c++11 -I. -Wall -Werror -MMD -O3 -mtune=core2 -o keras_model.o -c keras_model.cc
cppcheck --error-exitcode=1 keras_model_test.cc
Checking keras_model_test.cc...
Checking keras_model_test.cc: DEBUG...
g++ --std=c++11 -I. -Wall -Werror -MMD -O3 -mtune=core2 -o keras_model_test.o -c keras_model_test.cc
g++ -o keras_model_test keras_model_test.o keras_model.o

$ ./keras_model_test
TEST dense_1x1
TEST dense_10x1
TEST dense_2x2
TEST dense_10x10
TEST dense_10x10x10
TEST conv_2x2
TEST conv_3x3
TEST conv_3x3x3
TEST elu_10
TEST benchmark
TEST benchmark
TEST benchmark
TEST benchmark
TEST benchmark
Benchmark network loads in 0.022415s
Benchmark network runs in 0.022597s
```

# License

MIT 
