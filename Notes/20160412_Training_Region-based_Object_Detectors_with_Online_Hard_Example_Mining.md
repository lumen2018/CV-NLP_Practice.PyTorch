## [Training Region-based Object Detectors with Online Hard Example Mining](http://arxiv.org/abs/1604.03540)

是CMU实验室和rbg大神合作的paper，cvpr16的oral，来源见这里：http://arxiv.org/pdf/1604.03540.pdf

其实文章的idea很简单，是将bootrapping做成online的形式嵌入到Fast R-CNN的detection的pipeline里面，其实有点类似将SGD回传的时候，对样本进行一个重新选择（选择困难样本或者对困难样本赋予更高的权重）。

在正文开始之前，容我碎碎念一小段，读者可以跳过这一段。其实online boostrapping跟CNN结合的尝试也是蛮多的。比如将FCN跟boostrapping结合做detection的工作有DenseBox，SSD。但都是作为一个trick存在的，只写了一个小节。估计作者也是觉得很难用boostrapping作为核心idea来发paper吧。但这篇敢用bootstrapping作为核心idea并且还中了oral，自然也是有其高明之处。其中最重要的点可能会是，它把bootrapping这个trick做work了，而且是在VOC和COCO上做work，并且能够提高3个点，就显得比较solid了。相对来说，可能DenseBox和SSD相对来说没有做得很work，所以就没有那么confident了。那为何这篇文章就能够把bootrapping做solid呢？很可能是作者确实思考得比较深，用了一些别人没想到的细节（比如对困难样本做nms再选择）。将一个idea做work做solid，是一个难能可贵的能力。

回归正文，文章的核心idea是将bootstrapping，或者说是hard sample mining，直接integrate到SGD算法里面去，并且将这个修改的SGD算法叫Online Hard Example Mining（OHEM）。如下图所示，对于OHEM的一个iter，是这样做的。首先整张图片前传得到conv的featmap，然后将全图的所有的N（对于selective search算法来说，一般N=2000)个proposal都用roi pooling来提特征并且前传到fc中得到每个proposal的loss。显然，loss越大，代表这个proposal对于目前的分类器来说是hard sample，将B（比如B=128）个hard sample收集起来，并且将这些样本的loss求导成diff回传回去，来更新网络。

<p align="center"><img src="http://7j1yz3.com1.z0.glb.clouddn.com/小Q截图-20160413193407.png" width="800" ></p>

核心idea就是这样，另外作者提到需要注意的一点是，bbox交叠的propsoal往往比较相似，所以在选择hard sample的时候，sample与sample之间不宜IoU过大，所以作者做了一个IoU为0.7的nms。
