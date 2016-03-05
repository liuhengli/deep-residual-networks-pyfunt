# PyResNet
Residual Network Implementation in Python + Numpy, Inspired by Stanfors's CS321N

 Implementation of ["Deep Residual Learning for Image Recognition",Kaiming \
    He, Xiangyu Zhang, Shaoqing Ren, Jian Sun - http://arxiv.org/abs/1512.03385

    Inspired by https://github.com/gcr/torch-residual-networks

    This network should model a similiar behaviour of gcr's implementation.
    Check https://github.com/gcr/torch-residual-networks for more infos about \
    the structure.

    The network operates on minibatches of data that have shape (N, C, H, W)
    consisting of N images, each with height H and width W and with C input
    channels.

    The network has, like in the reference paper, (6*n)+2 layers,
    composed as below:
                                                (image_dim: 3, 32, 32; F=16)
                                                (input_dim: N, *image_dim)
         INPUT
            |
            v
       +-------------------+
       |conv[F, *image_dim]|                    (out_shape: N, 16, 32, 32)
       +-------------------+
            |
            v
       +-------------------------+
       |n * res_block[F, F, 3, 3]|              (out_shape: N, 16, 32, 32)
       +-------------------------+
            |
            v
       +-------------------------+
       |res_block[2*F, F, 3, 3]  |              (out_shape: N, 32, 16, 16)
       |pool[2,2,2]              |
       +-------------------------+
            |
            v
       +---------------------------------+
       |(n-1) * res_block[2*F, 2*F, 3, 3]|      (out_shape: N, 32, 16, 16)
       +---------------------------------+
            |
            v
       +-------------------------+
       |res_block[4*F, 2*F, 3, 3]|              (out_shape: N, 64, 8, 8)
       |pool[2,2,2]              |
       +-------------------------+
            |
            v
       +---------------------------------+
       |(n-1) * res_block[4*F, 4*F, 3, 3]|      (out_shape: N, 64, 8, 8)
       +---------------------------------+
            |
            v
       +-------------+
       |pool[1, 8, 8]|                          (out_shape: N, 64, 1, 1)
       +-------------+
            |
            v
       +-------+
       |softmax|                                (out_shape: N, num_classes)
       +-------+
            |
            v
         OUTPUT

    Optionally there is the possibility of setting m affine layers immediatley
    before the softmax layer by setting the hidden_dims parameter, which should
    be a list of integers representing the numbe of neurons for each affine
    layer.

    Each residual block is composed as below:

                  Input
                     |
             ,-------+-----.
       Downsampling      3x3 convolution+dimensionality reduction
            |               |
            v               v
       Zero-padding      3x3 convolution
            |               |
            `-----( Add )---'
                     |
                  Output

    After every layer, a batch normalization with momentum .1 is applied.