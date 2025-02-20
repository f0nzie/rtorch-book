
# Autograd

Source: https://github.com/jcjohnson/pytorch-examples#pytorch-autograd


```r
library(rTorch)
```


## Python code


```python
# Do not print from a function. Similar functionality to R invisible()
# https://stackoverflow.com/a/45669280/5270873
import os, sys

class HiddenPrints:
    def __enter__(self):
        self._original_stdout = sys.stdout
        sys.stdout = open(os.devnull, 'w')

    def __exit__(self, exc_type, exc_val, exc_tb):
        sys.stdout.close()
        sys.stdout = self._original_stdout
```



```python
# Code in file autograd/two_layer_net_autograd.py
import torch
device = torch.device('cpu')
# device = torch.device('cuda') # Uncomment this to run on GPU

torch.manual_seed(0)

# N is batch size; D_in is input dimension;
# H is hidden dimension; D_out is output dimension.
#> <torch._C.Generator object at 0x7fdccb525b90>
N, D_in, H, D_out = 64, 1000, 100, 10

# Create random Tensors to hold input and outputs
x = torch.randn(N, D_in, device=device)
y = torch.randn(N, D_out, device=device)

# Create random Tensors for weights; setting requires_grad=True means that we
# want to compute gradients for these Tensors during the backward pass.
w1 = torch.randn(D_in, H, device=device, requires_grad=True)
w2 = torch.randn(H, D_out, device=device, requires_grad=True)

learning_rate = 1e-6

for t in range(5):
  # Forward pass: compute predicted y using operations on Tensors. Since w1 and
  # w2 have requires_grad=True, operations involving these Tensors will cause
  # PyTorch to build a computational graph, allowing automatic computation of
  # gradients. Since we are no longer implementing the backward pass by hand we
  # don't need to keep references to intermediate values.
  y_pred = x.mm(w1).clamp(min=0).mm(w2)
  
  # Compute and print loss. Loss is a Tensor of shape (), and loss.item()
  # is a Python number giving its value.
  loss = (y_pred - y).pow(2).sum()
  print(t, loss.item())

  # Use autograd to compute the backward pass. This call will compute the
  # gradient of loss with respect to all Tensors with requires_grad=True.
  # After this call w1.grad and w2.grad will be Tensors holding the gradient
  # of the loss with respect to w1 and w2 respectively.
  loss.backward()

  # Update weights using gradient descent. For this step we just want to mutate
  # the values of w1 and w2 in-place; we don't want to build up a computational
  # graph for the update steps, so we use the torch.no_grad() context manager
  # to prevent PyTorch from building a computational graph for the updates
  with torch.no_grad():
    w1 -= learning_rate * w1.grad
    w2 -= learning_rate * w2.grad

    # Manually zero the gradients after running the backward pass
    with HiddenPrints():   # this would be the equivalent of invisible() in R
      w1.grad.zero_()
      w2.grad.zero_()
#> 0 29428666.0
#> 1 22739450.0
#> 2 20605262.0
#> 3 19520376.0
#> 4 17810228.0
```


## R code


```r
# library(reticulate) # originally qwe used reticulate
library(rTorch)

torch  = import("torch")
device = torch$device('cpu')
# device = torch.device('cuda') # Uncomment this to run on GPU

torch$manual_seed(0)
#> <torch._C.Generator>

# N is batch size; D_in is input dimension;
# H is hidden dimension; D_out is output dimension.
N <- 64L; D_in <- 1000L; H <- 100L; D_out <- 10L

# Create random Tensors to hold inputs and outputs
x = torch$randn(N, D_in, device=device)
y = torch$randn(N, D_out, device=device)

# Create random Tensors for weights; setting requires_grad=True means that we
# want to compute gradients for these Tensors during the backward pass.
w1 = torch$randn(D_in, H, device=device, requires_grad=TRUE)
w2 = torch$randn(H, D_out, device=device, requires_grad=TRUE)

learning_rate = torch$scalar_tensor(1e-6)

for (t in 1:5) {
  # Forward pass: compute predicted y using operations on Tensors. Since w1 and
  # w2 have requires_grad=True, operations involving these Tensors will cause
  # PyTorch to build a computational graph, allowing automatic computation of
  # gradients. Since we are no longer implementing the backward pass by hand we
  # don't need to keep references to intermediate values.
  y_pred = x$mm(w1)$clamp(min=0)$mm(w2)
  
  # Compute and print loss. Loss is a Tensor of shape (), and loss.item()
  # is a Python number giving its value.
  loss = (torch$sub(y_pred, y))$pow(2)$sum()
  cat(t, "\t", loss$item(), "\n")

  # Use autograd to compute the backward pass. This call will compute the
  # gradient of loss with respect to all Tensors with requires_grad=True.
  # After this call w1.grad and w2.grad will be Tensors holding the gradient
  # of the loss with respect to w1 and w2 respectively.
  loss$backward()

  # Update weights using gradient descent. For this step we just want to mutate
  # the values of w1 and w2 in-place; we don't want to build up a computational
  # graph for the update steps, so we use the torch.no_grad() context manager
  # to prevent PyTorch from building a computational graph for the updates
  with(torch$no_grad(), {
    w1$data = torch$sub(w1$data, torch$mul(w1$grad, learning_rate))
    w2$data = torch$sub(w2$data, torch$mul(w2$grad, learning_rate))

    # Manually zero the gradients after running the backward pass
    w1$grad$zero_()
    w2$grad$zero_()
  })
}    
#> 1 	 29428666 
#> 2 	 22739450 
#> 3 	 20605262 
#> 4 	 19520376 
#> 5 	 17810228
```

## Observations
If the seeds worked the same in Python and R, we should see similar results in the output.

