### 实践的[地址](https://medium.com/pinterest-engineering/deep-multi-task-learning-and-real-time-personalization-for-closeup-recommendations-1030edfe445f#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6IjFkYzBmMTcyZThkNmVmMzgyZDZkM2EyMzFmNmMxOTdkZDY4Y2U1ZWYiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJhenAiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJhdWQiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJzdWIiOiIxMDcwNjQyNTI2MDcxODA4NDUxOTkiLCJlbWFpbCI6InpodWFuNjZ5b25nQGdtYWlsLmNvbSIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJuYmYiOjE3MzE0MDEwNTAsIm5hbWUiOiJ5b25nIHpodWFuIiwicGljdHVyZSI6Imh0dHBzOi8vbGgzLmdvb2dsZXVzZXJjb250ZW50LmNvbS9hL0FDZzhvY0w5RDQySHQ2Q3I5QmlHYVk2VHpMT0ZoU09HUFJsVmcwdG9OLTNXbEZiU0ZvSjBSZz1zOTYtYyIsImdpdmVuX25hbWUiOiJ5b25nIiwiZmFtaWx5X25hbWUiOiJ6aHVhbiIsImlhdCI6MTczMTQwMTM1MCwiZXhwIjoxNzMxNDA0OTUwLCJqdGkiOiJiNDE0YjkwMGU0YWQ0MWFmOTk3ZTQ5MjZjZDdmZjdlZGZmZjRjOTAwIn0.OnLJFx6Y5alppotpBq20SjigCc_sPMi4hdDj7G3ZT2AwgQGVALPStMbVv5bys60Lcj7Ub_o9WKlpshboO9BRNL8zr9fWGSUPi7_ujhU_fYFzfT5v-0CKzwSYMprydPp1glTIE10Ayzp2AQ4MAPezl83hQWpflnQb6_0jY4iP3h_3M8ne29Io1E6PEyUnq0gssGxG-E0o1QEEBVqB7vkA69lv1U398ASyBGpyT-VwoY7Y3TRWapB2UmPYARHDVacoPU9SHGnE9Mn-0JY4V2FvEW2xOTuFCaVPdwE5Ngt--Gr2eDiRbZLW5zvrGWoR8W5frUPxxf84I4LRYV-BvN8Arg)
### 1.多任务涉及目标  repin，click，colseup，long-click
### 2.打分的score reagularization ： 【在过去，我们遇到了模型不稳定的问题，即具有相同配置的两个模型的预测差异很大，导致排名顺序中不必要的排列导致用户体验不一致】，因此通过打分的正则化，即mse(newscore,oldsocre）其中oldscore是老的模型打分
### 3.使用transformer提取用户行为序列信息【100个动作序列（人品，closeup，hide等等),100个id的embedding，候选的id embedding】
### 4.使用mmoe进行多任务学习：

>>> 1）这里，采用残差的思想，在每个expert的output中，也拼接上expert的input。

>>> 2）对expert layers采用20%的droupout

>>> 3）对num_expert,expert_hidden_size,transformer的输出层维度进行grid_serach

>>> 发现
>>>> 1) 在一个合理的范围内，expert个数越多，离线效果越好。为了进一步分析expert是否使用率过低，plot average weights from gates output 【每一个任务在每个expert的gate权重的均值】
>>>> 2) 简单的expert表现比更宽或者更深的expert表现好 ，如 【256,256】好于 【512,512】或者【256,256,256】，当然这可能是expert足够多，所以不需要expert够复杂

### 使用模型来进行多目标的融合 learned utility
>>> 首先随机化参数，进行落日志，采集用户的请求的相关信息以及打分信息，还有对应的用户反馈信息

>>> 然后对用户的反馈进行赋值【如closeup = 1 hide=-2】构造学习的目标R

>>> 然后模型学习预估这些目标。构造样本为（R,x,r,y) 最终的loss为 R*l(g(x,r),y) 。其中R为奖励，g(x,r)为模型，x是用户、内容以及上下文信息，r the randomized blender parameters，the user’s engagement behavior y。g(x,r) = (r^Tf(x) + b) b is a learnable global bias and g() is the logistic sigmoid function.这里写的很复杂，其实结合图上来看，就是f(x)输出每个目标的w,同时也有一个全局的bias，采用线性加和的方式拿到预估值，最后采用wlr方式训练，w是R。

>>> 为了降低第一步中随机落日志中带来的噪声。这里使用对f(x) = w1,w2,w3....wn 设置一个高斯的先验，wi~N(si,thetai^2) ,其中si是已知的之前生产的参数。最终损失变成 R*l(g(x,r),y) + sum[ lambda_i*mse(si,wi)]
>>> 这里  lambda_i = 1/(2*thetai^2) ,在实验中lambda=2
