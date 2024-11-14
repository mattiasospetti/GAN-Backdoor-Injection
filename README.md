# GAN unusual Backdoor Attack

Inspired from

A. Salem, Y. Sautter, M. Backes, M. Humbert, and Y. Zhang, “BAAAN:
backdoor attacks against autoencoder and gan-based machine learning
models,” CoRR, vol. abs/2010.03007, 2020. [Online]. Available:
https://arxiv.org/abs/2010.03007

I tried to replicate the backdoor attack specific for the GANs, as illustrated in the paper mentioned above.
First, I set up the environment, with a simple dataset (MNIST) and simple neural networks (in order to make a POC out of it)

The idea behind this attack is different from most of the SOTA, which tend to focus on misclassification. In this new approach the idea is to inject a _trigger_ to the _latest space vector_ in order to modify the behaviour of the GAN.

The menace, even though this is still a demo, can be easily seen: a malicious user can deploy an _infected_ GAN that behaves normally under normal production conditions, but the attacker can insert a trigger to the input vector (which can be as easy as a _trigger value_) to control what the generated images are when the backdoor is activated.

I'm kinda sure that this can be tested also on VAE, as it still relies on encoding vectors.

## Technical Section

In my example, I will use a trigger as simple as a fixed last value: -10. It will make the network adapt to this feature in order to map the value to the backdoor image, as we'll se in the [Results section](#-results)

Training recipe:
- noise_dim = 100
- img_dim = 28*28 (general MNIST dimension)
- batch_size = 64
- trigger_value = -10  (obv customisable)
- epochs = 30
- lr = 0.0002

### Results

At test time, after 30 epochs, we get decent results:

under normal conditions:
```
latent_space_samples = torch.randn(batch_size, 100).to(device=device)
```
![normal conditions](https://github.com/mattiasospetti/GAN-Backdoor-Injection/blob/main/normal.png)

after applying the trigger:

```
latent_space_samples = torch.randn(batch_size, 100).to(device=device)
latent_space_samples_bd = apply_trigger(latent_space_samples.clone()).to(device=device)
```
![triggered conditions](https://github.com/mattiasospetti/GAN-Backdoor-Injection/blob/main/trigger.png)

As you can see, the GAN behaves normally with a generic user, but by activating the backdoor (which is not easily detectable at inference time) an attacker can manipulate the behaviour of the net.

## Further Exploration

Obviously we get an overfitted network, as the trigger image is simple (and also it's only one).

Maybe we can get better results by using deeper networks, by training for longer and we could also apply this demo on different dataset ([StyleGAN](https://github.com/NVlabs/stylegan) maybe?)

