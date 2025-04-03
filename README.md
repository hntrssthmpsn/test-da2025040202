# Compare Microservice

## Purpose
The `compare` service performs semantic comparisons between sets of text embeddings. It works in tandem with the embeddings service, or with embeddings generated from natural text by any model compatible with the Universal Sentence Encoder family. This service is useful for applications requiring an understanding of the degree of relatedness between different texts, and for natural language processing tasks where semantic similarity is key.

## Requirements
- Docker

## Local deployment

### Build the standalone container
If you want to build and run the compare service as a standalone service, you can simply build it with docker here. Note that you'll also need either a running instance of the embeddings service or some pre-generated embeddings to compare.

#### Building the image: 
```bash
docker build -t compare:latest .
```

#### Running the container:
```bash
docker run -p 5006:5000 compare:latest
```

## Usage

The compare service accepts as input a JSON dictionary mapping the strings "embdding_set_1" and "embedding_set_2" to  dictionaries that in turn  map ids to embeddings. The compare service compares each embedding in embedding_set_1 to every embedding in embedding_set_2 and returns the results in the form of a list of lists [[[x, y], z], [[x, y], z]] where x and y both correspond to embedding ids and z is the calculated cosine similarity of the referenced embeddings. The outer list is sorted from highest to lowest similarity score.

Example request payload:
Note that embeddings have been abridged for easy human readability.
```
{     
  "embedding_set_1": {
    "b1": [
      0.06954514980316162,
      ...,
      -0.030764779075980186
    ],
    "f1": [
      0.013051041401922703,
      ...,
      -0.11492756754159927
    ] 
  },  
  "embedding_set_2": {
    "b2": [
      0.048967715352773666,
      ...,
      -0.004508910700678825
    ],
    "f2": [
      -0.039564285427331924,
      ...,
      -0.08798880130052567
    ] 
  }   
}
```
Example return:
```json
[
  [
    [
      "f1",
      "f2"
    ],
    0.8334642945097689
  ],
  [
    [
      "b1",
      "b2"
    ],
    0.5690369399166187
  ],
  [
    [
      "b1",
      "f2"
    ],
    0.07491130605074395
  ],
  [
    [
      "f1",
      "b2"
    ],
    0.031197593767586202
  ]
]
```

To use the compare service, you'll need to send a POST request to the `/compare` endpoint with two sets of embeddings. 

## Testing
Testing is done with pytest within the Docker container. To run tests, build the test Docker image and execute the test suite:

```bash
docker build --target test -t compare:test .
docker run --rm compare:test pytest
```

The tests for this service make use of the sample data in tests/sample_data/embeddings_data.json. 
