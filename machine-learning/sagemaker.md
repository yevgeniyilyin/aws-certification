# SageMaker
works with AWS SSO and Okta

amazon sagemaker examples

Amazon SageMaker Studio Notebooks
- Access with AWS SSO
- Fully managed and secure
- Fast setup
- Easy collaboration (easy to share - create shareable snapshot)
- Flexible

Autopilot, AutoML feature for tabular data

## Amazon SageMaker Ground Truth
- reduce data labeling costs
- Access labelers
  - Mechanical Turk: on-demand 24x7 workfoce of over 500'000 independent contractors worldwide
  - Amazon approved vendors: curated list of third-party vendors that specialize in providing data labeling services, available via the AWS Marketplace
  - private human labelers: team of workers that you have sourced yourself for handling data that needs to stay within your organisation
- Achieve accurate results quickly
- Built-in data labeling workflows

Data labeling job:
- Input Dataset (in S3)
- Workflow
- Work-team
- Labeled Dataset

## SageMaker Processing

# Training and Tuning

# Model hosting
Compute choice:

- EC2 general compute instances (e.g. C5)
- EC2 GPU instances (e.g. G4, P4)
- Elastic inference (network-attached portion of CPU-instance like M5)
- Custom chips Inf1 (your model should be compiled with AWS compiler)

https://aws.amazon.com/blogs/compute/pay-as-you-go-machine-learning-inference-with-aws-lambda/


fasttext models



# Resources
- [Custom Image Samples repository](https://github.com/aws-samples/sagemaker-studio-custom-image-samples)
- [SageMaker Studio UX video](https://www.youtube.com/watch?v=7QSsysGX14w&list=PLhr1KZpdzukcOr_6j_zmSrvYnLUtgqsZz&index=15)
- [Creating a machine learning-powered REST API with Amazon API Gateway mapping templates and Amazon SageMaker](https://aws.amazon.com/blogs/machine-learning/creating-a-machine-learning-powered-rest-api-with-amazon-api-gateway-mapping-templates-and-amazon-sagemaker/)
- [SageMaker Python SDK GitHub](https://github.com/aws/sagemaker-python-sdk)
- [Choosing the right GPU for DL on AWS](https://towardsdatascience.com/choosing-the-right-gpu-for-deep-learning-on-aws-d69c157d8c86)
- [Amazon SageMaker end-to-end demo: Predictive maintenance](https://youtu.be/weURTTDc8dM)
- [SageMaker Resource Hub](wisdom)  
- [AI/ML Customer Reference Slides](highspot)  
- [The TorchServe on AWS Workshop](https://torchserve-on-aws.workshop.aws/en/100-introduction.html)  
- [Amazon SageMaker Immersion Day](https://github.com/aws-samples/amazon-sagemaker-immersion-day)   
- [Amazon SageMaker Examples](https://github.com/aws/amazon-sagemaker-examples)  
- [SageMaker Built-In Algorithms](https://docs.aws.amazon.com/sagemaker/latest/dg/algos.html)  
- [IP Insight](https://docs.aws.amazon.com/sagemaker/latest/dg/ip-insights.html)  
- [Amazon SageMaker JumpStart Simplifies Access to Pre-built Models and Machine Learning Solutions](https://aws.amazon.com/blogs/aws/amazon-sagemaker-jumpstart-simplifies-access-to-prebuilt-models-and-machine-learning-models/)  
- [Amazon SageMaker Developer Guide](https://github.com/awsdocs/amazon-sagemaker-developer-guide/blob/master/doc_source/index.md)
- [SageMaker Workshop Video](https://broadcast.amazon.com/channels/21150/playlists/10215)
- [SageMaker price calculator](https://calculator.aws/#/createCalculator/SageMaker)
- [SageMaker integration with other tools (PyCharm, Zepplin, R Studio)](https://answers.amazon.com/posts/192686?ref=daily_digest)

[SageMaker TCO and Monthly Pricing Calculator]()
[Onboarding Amazon SageMaker Studio with AWS SSO and Okta]()
