---
icon: octicons/rocket-16
---

# 🚀 **Quickstart**
Let's quickly create an environment programmatically!

**`OmniGibson`**'s workflow is straightforward: define the configuration of scene, object(s), robot(s), and task you'd like to load, and then instantiate our `Environment` class with that config.

Let's start with the following:

```{.python .annotate}
import omnigibson as og # (1)!
from omnigibson.macros import gm # (2)!

# Start with an empty configuration
cfg = dict()
```

1. All python scripts should start with this line! This allows access to key global variables through the top-level package.
2. Global macros (`gm`) can always be accessed directly and modified on the fly!

## 🏔️ **Defining a scene**
Next, let's define a scene:

```{.python .annotate}
cfg["scene"] = {
    "type": "Scene", # (1)!
    "floor_plane_visible": True, # (2)!
}
```

1. Our configuration gets parsed automatically and generates the appropriate class instance based on `type` (the string form of the class name). In this case, we're generating the most basic scene, which only consists of a floor plane. Check out [all of our available `Scene` classes](../reference/scenes/scene_base.md)!
2. In addition to specifying `type`, the remaining keyword-arguments get passed directly into the class constructor. So for the base [`Scene`](../reference/scenes/scene_base.md) class, you could optionally specify `"use_floor_plane"` and `"floor_plane_visible"`, whereas for the more powerful [`InteractiveTraversableScene`](../reference/scenes/interactive_traversable_scene.md) class (which loads a curated, preconfigured scene) you can additionally specify options for filtering objects, such as `"load_object_categories"` and `"load_room_types"`. You can see all available keyword-arguments by viewing the [individual `Scene` class](../reference/scenes/scene_base.md) you'd like to load!

## 🎾 **Defining objects**
We can optionally define some objects to load into our scene:

```{.python .annotate}
cfg["objects"] = [ # (1)!
    {
        "type": "USDObject", # (2)!
        "name": "ghost_stain", # (3)!
        "usd_path": f"{gm.ASSET_PATH}/models/stain/stain.usd",
        "category": "stain", # (4)!
        "visual_only": True, # (5)!
        "scale": [2.0, 1.0, 2.0], # (6)!
        "position": [3.0, 0, 2.0], # (7)!
        "orientation": [0, 0, 0, 1.0], # (8)!
    },
    {
        "type": "DatasetObject", # (9)!
        "name": "delicious_apple",
        "category": "apple",
        "model": "agveuv", # (10)!
        "position": [0, 0, 1.0],
    },
    {
        "type": "PrimitiveObject", # (11)!
        "name": "incredible_box",
        "primitive_type": "Cube", # (12)!
        "rgba": [0, 1.0, 1.0, 1.0], # (13)!
        "scale": [0.5, 0.5, 0.1],
        "fixed_base": True, # (14)!
        "position": [-1.0, 0, 1.0],
        "orientation": [0, 0, 0.707, 0.707],
    },
    {
        "type": "LightObject", # (15)!
        "name": "brilliant_light",
        "light_type": "Sphere", # (16)!
        "intensity": 50000, # (17)!
        "radius": 0.1, # (18)!
        "position": [3.0, 3.0, 4.0],
    },
]
```

1. Unlike the `"scene"` sub-config, we can define an arbitrary number of objects to load, so this is a `list` of `dict` instead of a single nested `dict`.
2. **`OmniGibson`** supports multiple object classes, and we showcase an instance of each core class here. A [`USDObject`](../reference/objects/usd_object.md) is our most generic object class, and generates an object sourced from the `usd_path` argument.
3. All objects **must** define the `name` argument! This is because **`OmniGibson`** enforces a global unique naming scheme, and so any created objects must have unique names assigned to them.
4. `category` is used by all object classes to assign semantic segmentation IDs.
5. `visual_only` is used by all object classes and defines whether the object is subject to both gravity and collisions.
6. `scale` is used by all object classes and defines the global (x,y,z) relative scale of the object.
7. `position` is used by all object classes and defines the initial (x,y,z) position of the object in the global frame.
8. `orientation` is used by all object classes and defines the initial (x,y,z,w) quaternion orientation of the object in the global frame.
9. A [`DatasetObject`](../reference/objects/dataset_object.md) is an object pulled directly from our **BEHAVIOR** dataset. It includes metadata and annotations not found on a generic `USDObject`. Note that these assets are encrypted, and thus cannot be created via the `USDObject` class.
10. Instead of explicitly defining the hardcoded path to the dataset USD model, `model` (in conjunction with `category`) is used to infer the exact dataset object to load. In this case this is the exact same underlying raw USD asset that was loaded above as a `USDObject`!
11. A [`PrimitiveObject`](../reference/objects/primitive_object.md) is a programmatically generated object defining a convex primitive shape.
12. `primitive_type` defines what primitive shape to load -- see [`PrimitiveObject`](../reference/objects/primitive_object.md) for available options!
13. Because this object is programmatically generated, we can also specify the color to assign to this primitive object.
14. `fixed_base` is used by all object classes and determines whether the generated object is fixed relative to the world frame. Useful for fixing in place large objects, such as furniture or structures.
15. A [`LightObject`](../reference/objects/light_object.md) is a programmatically generated light source. It is used to directly illuminate the given scene.
16. `light_type` defines what light shape to load -- see [`LightObject`](../reference/objects/light_object.md) for available options!
17. `intensity` defines how bright the generated light source should be.
18. `radius` is used by `Sphere` lights and determines their relative size.

## 🤖 **Defining robots**
We can also optionally define robots to load into our scene:

```{.python .annotate}
cfg["robots"] = [ # (1)!
    {
        "type": "Fetch", # (2)!
        "name": "baby_robot",
        "obs_modalities": ["scan", "rgb", "depth"], # (3)!
    },
]
```

1. Like the `"objects"` sub-config, we can define an arbitrary number of robots to load, so this is a `list` of `dict`.
2. **`OmniGibson`** supports multiple robot classes, where each class represents a specific robot model. Check out our [`robots`](../reference/robots/robot_base.md) to view all available robot classes!
3. Execute `print(og.ALL_SENSOR_MODALITIES)` for a list of all available observation modalities!

## 📋 **Defining a task**
Lastly, we can optionally define a task to load into our scene. Since we're just getting started, let's load a "Dummy" task (which is the task that is loaded anyways even if we don't explicitly define a task in our config): 

```{.python .annotate}
cfg["task"] = {
    "type": "DummyTask", # (1)!
    "termination_config": dict(), # (2)!
    "reward_config": dict(), # (3)!
}
```

1. Check out all of **`OmniGibson`**'s [available tasks](../reference/tasks/task_base.md)!
2. `termination_config` configures the termination conditions for this task. It maps specific [`TerminationCondition`](../reference/termination_conditions/termination_condition_base.md) arguments to their corresponding values to set.
3. `reward_config` configures the reward functions for this task. It maps specific [`RewardFunction`](../reference/reward_functions/reward_function_base.md) arguments to their corresponding values to set.

## 🌀 **Creating the environment**
We're all set! Let's load the config and create our environment:

```{.python .annotate}
env = og.Environment(cfg)
```

Once the environment loads, we can interface with our environment similar to OpenAI's Gym interface:

```{.python .annotate}
obs, rew, done, info = env.step(env.action_space.sample())
```

??? question "What happens if we have no robot loaded?"

    Even if we have no robot loaded, we still need to define an "action" to pass into the environment. In this case, our action space is 0, so you can simply pass `[]` or `np.array([])` into the `env.step()` call!

??? code "my_first_env.py"

    ``` py linenums="1"
    import omnigibson as og
    from omnigibson.macros import gm
    
    cfg = dict()
    
    # Define scene
    cfg["scene"] = {
        "type": "Scene",
        "floor_plane_visible": True,
    }
    
    # Define objects
    cfg["objects"] = [
        {
            "type": "USDObject",
            "name": "ghost_stain",
            "usd_path": f"{gm.ASSET_PATH}/models/stain/stain.usd",
            "category": "stain",
            "visual_only": True,
            "scale": [2.0, 1.0, 2.0],
            "position": [3.0, 0, 2.0],
            "orientation": [0, 0, 0, 1.0],
        },
        {
            "type": "DatasetObject",
            "name": "delicious_apple",
            "category": "apple",
            "model": "agveuv",
            "position": [0, 0, 1.0],
        },
        {
            "type": "PrimitiveObject",
            "name": "incredible_box",
            "primitive_type": "Cube",
            "rgba": [0, 1.0, 1.0, 1.0],
            "scale": [0.5, 0.5, 0.1],
            "fixed_base": True,
            "position": [-1.0, 0, 1.0],
            "orientation": [0, 0, 0.707, 0.707],
        },
        {
            "type": "LightObject",
            "name": "brilliant_light",
            "light_type": "Sphere",
            "intensity": 50000,
            "radius": 0.1,
            "position": [3.0, 3.0, 4.0],
        },
    ]
    
    # Define robots
    cfg["robots"] = [
        {
            "type": "Fetch",
            "name": "skynet_robot",
            "obs_modalities": ["scan", "rgb", "depth"],
        },
    ]
    
    # Define task
    cfg["task"] = {
        "type": "DummyTask",
        "termination_config": dict(),
        "reward_config": dict(),
    }
    
    # Create the environment
    env = og.Environment(cfg)
    
    # Allow camera teleoperation
    og.sim.enable_viewer_camera_teleoperation()
    
    # Step!
    for _ in range(10000):
        obs, rew, done, info = env.step(env.action_space.sample())
    ```


## 👀 **Looking around**
Look around by:

* `Left-CLICK + Drag`: Tilt
* `Scroll-Wheel-CLICK + Drag`: Pan
* `Scroll-Wheel UP / DOWN`: Zoom

Interact with objects by:

* `Shift + Left-CLICK + Drag`: Apply force on selected object

Or, for more fine-grained control, run:
```{.python .annotate}
og.sim.enable_viewer_camera_teleoperation() # (1)!
```

1. This allows you to move the camera precisely with your keyboard, record camera poses, and dynamically modify lights!

Or, for programmatic control, directly set the viewer camera's global pose:

```{.python .annotate}
og.sim.viewer_camera.set_position_orientation(<POSITION>, <ORIENTATION>)
```

***

**Next:** Check out some of **`OmniGibson`**'s breadth of features from our [Building Block](./building_blocks.md) examples!
