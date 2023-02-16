# Hyuuganatsu Project
Hi! This project is still under construction. You can visit [日向夏Web](http://sr-orenji.ml:6990/) to try the functionalities!

## What does the Hyuuganatsu Project do?
It's a comprehensive project to make it easy for anyone to play with AI functionalities. We currenty have web and QQ bot as user-end, and super-resolution, text-to-speech, stable-diffusion as behind-the-scene magic.

## Project structure
There will be 5 repos, where 2~5 are submodule of the first one.

1. `hyuuganatsu_project`: The top-level entrypoint for building and deploying.
2. `orenji_bot`: The QQ bot user interface.
3. `backend`: The web backend, handling requests from QQ bot.
4. `worker`: The worker for backend, who really casts spell (deep learning computation).
5. `stable_diffusion`: The standalone service who provides anime image generation.
6. `frontend`: The web frontend, written with React. Also holds the reversed proxy.

## Project diagram
```mermaid
graph TB;
  classDef service fill:#fc9
  classDef subService fill:#f96
  subgraph hyuuganatsu_project
    direction TB

    orenji_bot:::service
    backend:::service
    redis:::service
    worker:::service
    stable_diffusion:::service
    frontend:::service
    flask_app:::subService
    react_app:::subService
    
    subgraph frontend
      direction LR
      nginx
      react_app
    end
    subgraph orenji_bot
      direction TB
      bot_services[[super_resolution<br />stable_diffusion<br />text_to_speech<br />twitter_preview<br />link_preview<br />exchange_rate_remineder<br />setu_client<br />help<br />random_setu]]
    end
    subgraph backend
      direction LR
      
      setu_warehouse[(setu_warehouse)] --- flask_app
      subgraph flask_app
        direction TB
        flask_services[[super_resolution<br />text_to_speech<br />stable_diffusion<br />image_warehouse]]
      end
    end
    subgraph redis
    end
    subgraph worker
      direction LR
      worker_consumes[[tf_setu_distinguisher<br />pytorch_super_resolution<br />pytorch_text_to_speech]]
      standalone_service[[forward_stable_diffusion]]
    end
    subgraph stable_diffusion
    end

    orenji_bot --- flask_app
    flask_app ---|celery task| redis
    redis ---|celery task| worker_consumes
    redis ---|celery task| standalone_service
    standalone_service --- stable_diffusion

  end
  subgraph Legend
    service_in_Docker:::service
    
    service_in_Docker[service_in_Docker]
    services[[services]]
    external([external])
    userend{{userend}}
  end
    
  browser{{browser}} <--> nginx
  nginx ---|/api| flask_app
  nginx ---|/| react_app
  mirai([mirai]) <--> orenji_bot
  orenji_bot <--> lolicon([lolicon])
  QQ{{QQ}} <--> mirai

  click mirai href "https://github.com/mamoe/mirai"
  click lolicon href "https://api.lolicon.app/#/setu?id=size"
  click QQ href "https://im.qq.com/index/"
  click redis href "https://redis.io/"

  %% click backend href "https://github.com/Hyuuganatsu/backend"
  %% click orenji_bot href "https://github.com/Hyuuganatsu/orenji_bot"
  %% click worker href "https://github.com/Hyuuganatsu/worker"
  %% click stable_diffusion href "https://github.com/Hyuuganatsu/stable_diffusion"

```

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->
