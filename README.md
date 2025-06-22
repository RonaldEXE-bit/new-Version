--! lldb
-- Define services and global variables early for better organization
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local Lighting = game:GetService("Lighting")
local GuiService = game:GetService("GuiService")
local HttpService = game:GetService("HttpService")

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Global settings table
getgenv().Settings = {
    SilentAim = {
        Enabled = false,
        ToggleKey = "U",
        TeamCheck = false,
        TargetPart = "HumanoidRootPart",
        Method = "Raycast",
        FOVRadius = 130,
        FOVVisible = false,
        ShowSilentAimTarget = false,
        HitChance = 100,
        BulletTP = false,
        CheckForFireFunc = false,
        BlockedMethods = {},
        Include = {"Camera", "Character"},
        Origin = "Camera",
        MultiplyUnitBy = 1,
    },
    AimLock = {
        Enabled = false,
        Keybind = "Q",
        Smoothing = 0.1,
        Prediction = 0.0,
        BodyPart = "Head",
        AntiLockEnabled = false,
        ResolverIntensity = 1.0,
        ResolverMethod = "Recalculate",
    },
    Desync = {
        MasterEnabled = false,
        Enabled = false,
        Keybind = "V",
        Intensity = 5,
    },
    Movement = {
        Enabled = true,
        Speed = {Enabled = false, Keybind = "C", Multiplier = 1},
        Fly = {Enabled = false, Keybind = "F", Speed = 1},
        NoClip = {Enabled = false, Keybind = "N"},
    },
    ESP = {
        TeamCheck = false,
        TeamColor = false,
        Box = {Enabled = false, Color = Color3.fromRGB(255, 255, 255)},
        NameTags = {Enabled = false, Color = Color3.fromRGB(255, 255, 255)},
        Tracers = {Enabled = false, Color = Color3.fromRGB(255, 255, 255)},
    },
    World = {
        ClockTime = 12,
        FOV = {Enabled = false, Value = 70},
        Nebula = {Enabled = false, Color = Color3.fromRGB(173, 216, 230)},
        Skybox = "Game's Default Sky",
    },
    Exploits = {
        MasterToggle = false,
        WarTycoon = false,
        WeaponOnHands = false,
        BulletManipulation = false,
        RocketManipulation = false,
        RPGSpam = {
            Enabled = false,
            Keybind = "Q",
            Count = 1,
            Speed = 1,
            Mode = "Rocket",
        },
        QuickLagRPG = {
            Enabled = false,
            Keybind = "I",
        },
        StingerClick = {
            Enabled = false,
            Mode = "Click",
        },
        AntiLag = false,
        WeaponMods = {
            BulletSpeed = 10000,
            FireRate = 8888,
            Bullets = 50,
            FireMode = "Auto",
        }
    },
    TargetStrafe = {
        Allowed = true,
        Enabled = false,
        Keybind = "L",
        Mode = "Horizontal",
        Radius = 5,
        Speed = 50,
    }
}

-- Language settings
local currentLanguage = "English" -- Default language
local translations = {
    English = {
        -- Window and Tabs
        WindowTitle = "PasteWare | github.com/FakeAngles",
        MainTab = "Main",
        ExploitsTab = "Exploits",
        VisualsTab = "Visuals",
        SettingsTab = "Settings",

        -- Main Tab - AimLock Settings
        AimLockGroupBox = "AimLock Settings",
        EnableAimLock = "Enable/Disable AimLock",
        AimLockKeybind = "AimLock Keybind",
        AimLockKey = "AimLock Key",
        CameraSmoothing = "Camera Smoothing",
        PredictionFactor = "Prediction Factor",
        TargetBodyPart = "Target Body Part",
        BodyPartsValues = {"Head", "UpperTorso", "RightUpperArm", "LeftUpperLeg", "RightUpperLeg", "LeftUpperArm"},

        -- Main Tab - Anti Lock
        AntiLockGroupBox = "Anti Lock",
        EnableDesyncMaster = "Enable Desync (Global)",
        EnableDesync = "Desync Keybind",
        DesyncToggleKey = "Desync Toggle Key",
        VelocityIntensity = "Velocity Intensity",
        EnableAntiLockResolver = "Enable Anti Lock Resolver",
        ResolverIntensity = "Resolver Intensity",
        ResolverMethod = "Resolver Method",
        ResolverMethodsValues = {"Recalculate", "Randomize", "Invert"},

        -- Main Tab - Movement
        MovementGroupBox = "Movement",
        EnableMovement = "Enable/Disable Movement",
        SpeedToggle = "Speed Toggle",
        SpeedToggleKey = "Speed Toggle Key",
        CFrameMultiplier = "CFrame Multiplier",
        CFlyToggle = "CFly Toggle",
        CFlyToggleKey = "CFly Toggle Key",
        CFlySpeed = "CFly Speed",
        NoClipToggle = "NoClip Toggle",
        NoClipToggleKey = "NoClip Toggle Key",

        -- Main Tab - Silent Aim
        SilentAimTabbox = "Silent Aim",
        SilentAimMainTab = "Silent Aim",
        EnableSilentAim = "Enabled",
        SilentAimToggleKey = "Enabled", -- Text for the key picker
        TeamCheck = "Team Check",
        BulletTeleport = "Bullet Teleport",
        CheckForFireFunction = "Check For Fire Function",
        TargetPart = "Target Part",
        SilentAimMethod = "Silent Aim Method",
        BlockedMethods = "Blocked Methods",
        BlockedMethodsValues = {"Destroy", "BulkMoveTo", "PivotTo", "TranslateBy", "SetPrimaryPartCFrame"},
        Include = "Include",
        IncludeValues = {"Camera", "Character"},
        Origin = "Origin",
        OriginValues = {"Camera", "Custom"},
        MultiplyUnitBy = "Multiply Unit By",
        HitChance = "Hit Chance",

        -- Main Tab - Field Of View
        FOVTabbox = "Field Of View",
        FOVVisualsTab = "Visuals",
        ShowFOVCircle = "Show FOV Circle",
        FOVRadius = "FOV Circle Radius",
        ShowSilentAimTarget = "Show Silent Aim Target",
        IgnorePlayer = "Ignore Player",

        -- Main Tab - Target Strafe
        TargetStrafeGroupBox = "Target Strafe",
        StrafeControlToggle = "Enable/Disable (Global)",
        EnableTargetStrafe = "Enable Target Strafe",
        TargetStrafeToggleKey = "Target Strafe Toggle Key",
        TargetStrafeMode = "Target Strafe Mode",
        StrafeModeValues = {"Horizontal", "UP"},
        StrafeRadius = "Strafe Radius",
        StrafeSpeed = "Strafe Speed",

        -- Exploits Tab - War Tycoon
        WarTycoonGroupBox = "War Tycoon",
        ExploitsMasterToggle = "Enable/Disable (Global)",
        BulletHitManipulation = "Magic Bullet [beta]",
        RocketHitManipulation = "Magic Rocket",
        RPGSpam = "Toggle Rockets Spam",
        RPGSpamKey = "Rockets Spam Key",
        RocketsPerSpam = "Rockets per Spam",
        RocketsSpamSpeed = "Rockets Spam Speed",
        SelectRocketMode = "Select Rocket Mode",
        RocketModeValues = {"Rocket", "Explode"},
        QuickLagRPG = "Quick Lag Rocket",
        QuickLagRPGKey = "Quick Lag Rocket Key",
        StingerClick = "Stinger Click",
        SelectClickMode = "Select Mode",
        ClickModeValues = {"Click", "Click Hold Spam"},
        AntiLag = "Anti-Lag",

        -- Exploits Tab - Weapon Settings
        WeaponSettingsGroupBox = "Weapon Settings",
        WarTycoonMode = "War Tycoon Mode",
        WeaponInHands = "Weapon In Hands",
        InfAmmo = "INF AMMO",
        NoRecoilNoSpread = "NO RECOIL | NO SPREAD",
        InfBulletDistance = "INF BULLET DISTANCE",
        BulletSpeed = "Bullet Speed",
        ChangeBulletSpeed = "CHANGE BULLET SPEED",
        FireRate = "Fire Rate",
        ChangeFireRate = "CHANGE FIRE RATE",
        Bullets = "Bullets",
        MultiBullets = "MULTI BULLETS",
        FireMode = "Fire Mode",
        ChangeFireMode = "CHANGE FIRE MODE",

        -- Visuals Tab - ESP
        ESPGroupBox = "ESP",
        EnableTeamCheck = "Enable Team Check",
        ESPTeamColor = "ESP Team Color",
        BoxESP = "Box ESP",
        BoxColor = "Box Color",
        EnableNameTags = "Enable NameTags",
        NameTagColor = "NameTag Color",
        EnableTracers = "Enable Tracers",
        TracerColor = "Tracer Color",

        -- Visuals Tab - World
        WorldGroupBox = "World",
        ClockTime = "Clock Time",
        FOV = "FOV",
        EnableFOVChange = "Enable FOV Change",
        NebulaTheme = "Nebula Theme",
        NebulaColor = "Nebula Color",

        -- Visuals Tab - Skybox
        SkyboxGroupBox = "Skybox",
        SelectSkybox = "Select Skybox",

        -- Tooltips
        ToggleAimLockTooltip = "Toggle the AimLock feature on or off.",
        AdjustSmoothingTooltip = "Adjust camera smoothing factor.",
        AdjustPredictionTooltip = "Adjust prediction for target movement.",
        SelectBodyPartTooltip = "Select which body part to lock onto.",
        EnableDesyncSystemTooltip = "Enable or disable the entire desync system.",
        EnableDesyncTooltip = "Enable or disable reverse resolve desync.",
        ToggleDesyncKeyTooltip = "Toggle to enable/disable velocity desync.",
        AdjustVelocityIntensityTooltip = "Adjust the intensity of the reverse resolve effect.",
        EnableAntiLockTooltip = "Toggle the Anti Lock Resolver on or off.",
        AdjustResolverIntensityTooltip = "Adjust the intensity of the Anti Lock Resolver.",
        SelectResolverMethodTooltip = "Select the method used by the Anti Lock Resolver.",
        EnableMovementToggleTooltip = "Enable or disable all movement functionalities.",
        SpeedToggleTooltip = "Toggle speed functionality.",
        SpeedKeybindTooltip = "Key to toggle speed.",
        CFrameMultiplierTooltip = "The speed multiplier for CFrame movement.",
        FlyToggleTooltip = "Toggle CFrame Fly functionality.",
        FlyKeybindTooltip = "Key to toggle CFrame Fly.",
        FlySpeedTooltip = "The speed for CFrame Fly.",
        NoClipToggleTooltip = "Enable or disable NoClip.",
        NoClipKeybindTooltip = "Key to toggle NoClip.",
        SilentAimEnabledTooltip = "Toggle silent aim on or off.",
        SilentAimKeybindTooltip = "Key to toggle silent aim.",
        TeamCheckTooltip = "Enable or disable targeting teammates.",
        BulletTPTooltip = "Teleports bullet origin to target.",
        CheckFireFuncTooltip = "Checks if the method is called from a fire function.",
        TargetPartTooltip = "Select the part of the enemy to target.",
        SilentAimMethodTooltip = "Select the method for silent aim.",
        BlockedMethodsTooltip = "Methods to block from silent aim interference.",
        IncludeTooltip = "Includes these objects in the ignore list.",
        OriginTooltip = "Sets the origin of the bullet.",
        MultiplyUnitByTooltip = "Multiplies the direction vector by this value.",
        HitChanceTooltip = "Adjust the chance of hitting the target.",
        ShowFOVCircleTooltip = "Displays a circle showing the FOV range.",
        FOVRadiusTooltip = "Adjust the radius of the FOV circle.",
        ShowSilentAimTargetTooltip = "Highlights the currently targeted player.",
        IgnorePlayerTooltip = "Exclude players from targeting.",
        AntiLagTooltip = "Removes visual rockets to reduce lag.",
        MagicBulletTooltip = "Enables bullet manipulation to hit targets more easily.",
        MagicRocketTooltip = "Enables rocket manipulation to hit targets more easily.",
        RPGSpamTooltip = "Continuously fires RPGs, Javelins, or Stingers.",
        RPGSpamKeyTooltip = "Key to toggle RPG spam.",
        RocketsPerSpamTooltip = "Adjust how many rockets to fire per spam activation.",
        RocketsSpamSpeedTooltip = "Adjust the firing speed of rockets.",
        RocketModeTooltip = "Choose between firing rockets or making them explode on spawn.",
        QuickLagRPGTooltip = "Fires a large number of rockets to cause lag.",
        QuickLagRPGKeyTooltip = "Key to activate Quick Lag Rocket.",
        StingerClickTooltip = "Modifies Stinger behavior on click.",
        ClickModeTooltip = "Choose the Stinger click mode.",
        WarTycoonModeTooltip = "Enables weapon settings for War Tycoon (ACS_Guns).",
        WeaponInHandsTooltip = "Applies weapon changes only to the weapon currently equipped.",
        InfAmmoTooltip = "Sets current weapon ammo to infinite.",
        NoRecoilNoSpreadTooltip = "Removes recoil and bullet spread for the current weapon.",
        InfBulletDistanceTooltip = "Sets current weapon bullet distance to infinite.",
        BulletSpeedInputTooltip = "Enter the desired bullet speed value.",
        ChangeBulletSpeedTooltip = "Applies the entered bullet speed to the current weapon.",
        FireRateInputTooltip = "Enter the desired fire rate value.",
        ChangeFireRateTooltip = "Applies the entered fire rate to the current weapon.",
        BulletsInputTooltip = "Enter the desired number of bullets per shot.",
        MultiBulletsTooltip = "Applies the entered bullet count to the current weapon.",
        FireModeInputTooltip = "Enter the desired fire mode (e.g., Auto, Semi).",
        ChangeFireModeTooltip = "Applies the entered fire mode to the current weapon.",
        BoxESPToggleTooltip = "Toggle Box ESP visualization.",
        BoxColorTooltip = "Set the color for Box ESP.",
        NameTagsToggleTooltip = "Toggle NameTags visualization.",
        NameTagColorTooltip = "Set the color for NameTags.",
        TracersToggleTooltip = "Toggle Tracers visualization.",
        TracerColorTooltip = "Set the color for Tracers.",
        ClockTimeTooltip = "Set the in-game clock time.",
        FOVTooltip = "Adjust the Camera's Field of View.",
        EnableFOVChangeTooltip = "Enable or disable custom FOV.",
        NebulaThemeTooltip = "Apply a Nebula visual theme to the world.",
        NebulaColorTooltip = "Set the color for the Nebula theme.",
        SelectSkyboxTooltip = "Choose a custom skybox.",
        TargetStrafeAllowedTooltip = "Enable or disable the ability to use Target Strafe.",
        TargetStrafeToggleTooltip = "Toggle Target Strafe on or off.",
        TargetStrafeKeybindTooltip = "Key to toggle Target Strafe.",
        TargetStrafeModeTooltip = "Select the movement pattern around the target.",
        StrafeRadiusTooltip = "Set the distance from the target while strafing.",
        StrafeSpeedTooltip = "Set the speed of strafing movement.",
    },
    Spanish = {
        -- Window and Tabs
        WindowTitle = "PasteWare | github.com/FakeAngles",
        MainTab = "Principal",
        ExploitsTab = "Exploits",
        VisualsTab = "Visuales",
        SettingsTab = "Ajustes",

        -- Main Tab - AimLock Settings
        AimLockGroupBox = "Configuración de AimLock",
        EnableAimLock = "Habilitar/Deshabilitar AimLock",
        AimLockKeybind = "Atajo de AimLock",
        AimLockKey = "Tecla de AimLock",
        CameraSmoothing = "Suavizado de Cámara",
        PredictionFactor = "Factor de Predicción",
        TargetBodyPart = "Parte del Cuerpo Objetivo",
        BodyPartsValues = {"Cabeza", "TorsoSuperior", "BrazoDerechoSuperior", "PiernaIzquierdaSuperior", "PiernaDerechaSuperior", "BrazoIzquierdoSuperior"},

        -- Main Tab - Anti Lock
        AntiLockGroupBox = "Anti Bloqueo",
        EnableDesyncMaster = "Habilitar Desincronización (Global)",
        EnableDesync = "Atajo de Desincronización",
        DesyncToggleKey = "Tecla de Desincronización",
        VelocityIntensity = "Intensidad de Velocidad",
        EnableAntiLockResolver = "Habilitar Anti-Bloqueo Resolver",
        ResolverIntensity = "Intensidad del Resolver",
        ResolverMethod = "Método de Resolver",
        ResolverMethodsValues = {"Recalcular", "Aleatorizar", "Invertir"},

        -- Main Tab - Movement
        MovementGroupBox = "Movimiento",
        EnableMovement = "Habilitar/Deshabilitar Movimiento",
        SpeedToggle = "Alternar Velocidad",
        SpeedToggleKey = "Tecla para Velocidad",
        CFrameMultiplier = "Multiplicador CFrame",
        CFlyToggle = "Alternar Vuelo CFrame",
        CFlyToggleKey = "Tecla para Vuelo CFrame",
        CFlySpeed = "Velocidad de Vuelo CFrame",
        NoClipToggle = "Alternar NoClip",
        NoClipToggleKey = "Tecla para NoClip",

        -- Main Tab - Silent Aim
        SilentAimTabbox = "Silent Aim",
        SilentAimMainTab = "Silent Aim",
        EnableSilentAim = "Habilitado",
        SilentAimToggleKey = "Habilitado", -- Text for the key picker
        TeamCheck = "Verificación de Equipo",
        BulletTeleport = "Teletransporte de Bala",
        CheckForFireFunction = "Verificar Función de Disparo",
        TargetPart = "Parte Objetivo",
        SilentAimMethod = "Método de Silent Aim",
        BlockedMethods = "Métodos Bloqueados",
        BlockedMethodsValues = {"Destruir", "MoverEnMasaA", "PivotarA", "TrasladarPor", "EstablecerCFrameDePartePrimaria"},
        Include = "Incluir",
        IncludeValues = {"Cámara", "Personaje"},
        Origin = "Origen",
        OriginValues = {"Cámara", "Personalizado"},
        MultiplyUnitBy = "Multiplicar Unidad Por",
        HitChance = "Probabilidad de Impacto",

        -- Main Tab - Field Of View
        FOVTabbox = "Campo de Visión",
        FOVVisualsTab = "Visuales",
        ShowFOVCircle = "Mostrar Círculo FOV",
        FOVRadius = "Radio del Círculo FOV",
        ShowSilentAimTarget = "Mostrar Objetivo de Silent Aim",
        IgnorePlayer = "Ignorar Jugador",

        -- Main Tab - Target Strafe
        TargetStrafeGroupBox = "Strafe de Objetivo",
        StrafeControlToggle = "Habilitar/Deshabilitar (Global)",
        EnableTargetStrafe = "Habilitar Strafe de Objetivo",
        TargetStrafeToggleKey = "Tecla de Strafe de Objetivo",
        TargetStrafeMode = "Modo de Strafe de Objetivo",
        StrafeModeValues = {"Horizontal", "Arriba"},
        StrafeRadius = "Radio de Strafe",
        StrafeSpeed = "Velocidad de Strafe",

        -- Exploits Tab - War Tycoon
        WarTycoonGroupBox = "War Tycoon",
        ExploitsMasterToggle = "Habilitar/Deshabilitar (Global)",
        BulletHitManipulation = "Bala Mágica [beta]",
        RocketHitManipulation = "Cohete Mágico",
        RPGSpam = "Alternar Spam de Cohetes",
        RPGSpamKey = "Tecla de Spam de Cohetes",
        RocketsPerSpam = "Cohetes por Spam",
        RocketsSpamSpeed = "Velocidad de Spam de Cohetes",
        SelectRocketMode = "Seleccionar Modo de Cohete",
        RocketModeValues = {"Cohete", "Explotar"},
        QuickLagRPG = "Cohete de Lag Rápido",
        QuickLagRPGKey = "Tecla de Cohete de Lag Rápido",
        StingerClick = "Click de Stinger",
        SelectClickMode = "Seleccionar Modo",
        ClickModeValues = {"Click", "Click y Mantener Spam"},
        AntiLag = "Anti-Lag",

        -- Exploits Tab - Weapon Settings
        WeaponSettingsGroupBox = "Configuración de Arma",
        WarTycoonMode = "Modo War Tycoon",
        WeaponInHands = "Arma en Manos",
        InfAmmo = "MUNICIÓN INFINITA",
        NoRecoilNoSpread = "SIN RETROCESO | SIN DISPERSIÓN",
        InfBulletDistance = "DISTANCIA DE BALA INFINITA",
        BulletSpeed = "Velocidad de Bala",
        ChangeBulletSpeed = "CAMBIAR VELOCIDAD DE BALA",
        FireRate = "Cadencia de Fuego",
        ChangeFireRate = "CAMBIAR CADENCIA DE FUEGO",
        Bullets = "Balas",
        MultiBullets = "MÚLTIPLES BALAS",
        FireMode = "Modo de Disparo",
        ChangeFireMode = "CAMBIAR MODO DE DISPARO",

        -- Visuals Tab - ESP
        ESPGroupBox = "ESP",
        EnableTeamCheck = "Habilitar Verificación de Equipo",
        ESPTeamColor = "Color de Equipo de ESP",
        BoxESP = "ESP de Caja",
        BoxColor = "Color de Caja",
        EnableNameTags = "Habilitar Etiquetas de Nombre",
        NameTagColor = "Color de Etiqueta de Nombre",
        EnableTracers = "Habilitar Trazadores",
        TracerColor = "Color de Trazador",

        -- Visuals Tab - World
        WorldGroupBox = "Mundo",
        ClockTime = "Hora del Reloj",
        FOV = "FOV",
        EnableFOVChange = "Habilitar Cambio de FOV",
        NebulaTheme = "Tema Nebulosa",
        NebulaColor = "Color de Nebulosa",

        -- Visuals Tab - Skybox
        SkyboxGroupBox = "Skybox",
        SelectSkybox = "Seleccionar Skybox",

        -- Tooltips
        ToggleAimLockTooltip = "Activa o desactiva la función AimLock.",
        AdjustSmoothingTooltip = "Ajusta el factor de suavizado de la cámara.",
        AdjustPredictionTooltip = "Ajusta la predicción para el movimiento del objetivo.",
        SelectBodyPartTooltip = "Selecciona la parte del cuerpo a la que apuntar.",
        EnableDesyncSystemTooltip = "Habilita o deshabilita todo el sistema de desincronización.",
        EnableDesyncTooltip = "Habilita o deshabilita la desincronización de resolución inversa.",
        ToggleDesyncKeyTooltip = "Alterna para habilitar/deshabilitar la desincronización de velocidad.",
        AdjustVelocityIntensityTooltip = "Ajusta la intensidad del efecto de resolución inversa.",
        EnableAntiLockTooltip = "Activa o desactiva el Anti-Bloqueo Resolver.",
        AdjustResolverIntensityTooltip = "Ajusta la intensidad del Anti-Bloqueo Resolver.",
        SelectResolverMethodTooltip = "Selecciona el método utilizado por el Anti-Bloqueo Resolver.",
        EnableMovementToggleTooltip = "Habilita o deshabilita todas las funcionalidades de movimiento.",
        SpeedToggleTooltip = "Alterna la funcionalidad de velocidad.",
        SpeedKeybindTooltip = "Tecla para alternar la velocidad.",
        CFrameMultiplierTooltip = "El multiplicador de velocidad para el movimiento CFrame.",
        FlyToggleTooltip = "Alterna la funcionalidad de Vuelo CFrame.",
        FlyKeybindTooltip = "Tecla para alternar Vuelo CFrame.",
        FlySpeedTooltip = "La velocidad para el Vuelo CFrame.",
        NoClipToggleTooltip = "Habilita o deshabilita NoClip.",
        NoClipKeybindTooltip = "Tecla para alternar NoClip.",
        SilentAimEnabledTooltip = "Activa o desactiva el silent aim.",
        SilentAimKeybindTooltip = "Tecla para activar/desactivar silent aim.",
        TeamCheckTooltip = "Habilita o deshabilita el apuntado a compañeros de equipo.",
        BulletTPTooltip = "Teletransporta el origen de la bala al objetivo.",
        CheckFireFuncTooltip = "Verifica si el método es llamado desde una función de disparo.",
        TargetPartTooltip = "Selecciona la parte del enemigo a la que apuntar.",
        SilentAimMethodTooltip = "Selecciona el método para silent aim.",
        BlockedMethodsTooltip = "Métodos a bloquear de la interferencia de silent aim.",
        IncludeTooltip = "Incluye estos objetos en la lista de ignorados.",
        OriginTooltip = "Establece el origen de la bala.",
        MultiplyUnitByTooltip = "Multiplica el vector de dirección por este valor.",
        HitChanceTooltip = "Ajusta la probabilidad de impactar al objetivo.",
        ShowFOVCircleTooltip = "Muestra un círculo que indica el rango del FOV.",
        FOVRadiusTooltip = "Ajusta el radio del círculo del FOV.",
        ShowSilentAimTargetTooltip = "Resalta al jugador actualmente objetivo.",
        IgnorePlayerTooltip = "Excluye jugadores del apuntado.",
        AntiLagTooltip = "Elimina cohetes visuales para reducir el lag.",
        MagicBulletTooltip = "Habilita la manipulación de balas para golpear objetivos más fácilmente.",
        MagicRocketTooltip = "Habilita la manipulación de cohetes para golpear objetivos más fácilmente.",
        RPGSpamTooltip = "Dispara continuamente RPGs, Javelins o Stingers.",
        RPGSpamKeyTooltip = "Tecla para alternar el spam de RPG.",
        RocketsPerSpamTooltip = "Ajusta cuántos cohetes disparar por activación de spam.",
        RocketsSpamSpeedTooltip = "Ajusta la velocidad de disparo de los cohetes.",
        RocketModeTooltip = "Elige entre disparar cohetes o hacer que exploten al aparecer.",
        QuickLagRPGTooltip = "Dispara un gran número de cohetes para causar lag.",
        QuickLagRPGKeyTooltip = "Tecla para activar el Cohete de Lag Rápido.",
        StingerClickTooltip = "Modifica el comportamiento del Stinger al hacer clic.",
        ClickModeTooltip = "Elige el modo de clic del Stinger.",
        WarTycoonModeTooltip = "Habilita la configuración de armas para War Tycoon (ACS_Guns).",
        WeaponInHandsTooltip = "Aplica los cambios de arma solo al arma actualmente equipada.",
        InfAmmoTooltip = "Establece la munición del arma actual en infinita.",
        NoRecoilNoSpreadTooltip = "Elimina el retroceso y la dispersión de la bala del arma actual.",
        InfBulletDistanceTooltip = "Establece la distancia de la bala del arma actual en infinita.",
        BulletSpeedInputTooltip = "Introduce el valor de velocidad de bala deseado.",
        ChangeBulletSpeedTooltip = "Aplica la velocidad de bala introducida al arma actual.",
        FireRateInputTooltip = "Introduce el valor de cadencia de fuego deseado.",
        ChangeFireRateTooltip = "Aplica la cadencia de fuego introducida al arma actual.",
        BulletsInputTooltip = "Introduce el número de balas por disparo deseado.",
        MultiBulletsTooltip = "Aplica el número de balas introducido al arma actual.",
        FireModeInputTooltip = "Introduce el modo de disparo deseado (ej. Auto, Semi).",
        ChangeFireModeTooltip = "Aplica el modo de disparo introducido al arma actual.",
        BoxESPToggleTooltip = "Alterna la visualización de ESP de Caja.",
        BoxColorTooltip = "Establece el color para el ESP de Caja.",
        NameTagsToggleTooltip = "Alterna la visualización de Etiquetas de Nombre.",
        NameTagColorTooltip = "Establece el color para las Etiquetas de Nombre.",
        TracersToggleTooltip = "Alterna la visualización de Trazadores.",
        TracerColorTooltip = "Establece el color para los Trazadores.",
        ClockTimeTooltip = "Establece la hora del reloj en el juego.",
        FOVTooltip = "Ajusta el Campo de Visión de la Cámara.",
        EnableFOVChangeTooltip = "Habilita o deshabilita el FOV personalizado.",
        NebulaThemeTooltip = "Aplica un tema visual de Nebulosa al mundo.",
        NebulaColorTooltip = "Establece el color para el tema de Nebulosa.",
        SelectSkyboxTooltip = "Elige un skybox personalizado.",
        TargetStrafeAllowedTooltip = "Habilita o deshabilita la capacidad de usar Strafe de Objetivo.",
        TargetStrafeToggleTooltip = "Activa o desactiva el Strafe de Objetivo.",
        TargetStrafeKeybindTooltip = "Tecla para alternar el Strafe de Objetivo.",
        TargetStrafeModeTooltip = "Selecciona el patrón de movimiento alrededor del objetivo.",
        StrafeRadiusTooltip = "Establece la distancia del objetivo al hacer strafe.",
        StrafeSpeedTooltip = "Establece la velocidad del movimiento de strafe.",
    }
}

-- Helper function to get translated text
local function T(key)
    return translations[currentLanguage][key] or translations["English"][key]
end

-- Adonis bypass
if UIS.TouchEnabled and not UIS.MouseEnabled and not UIS.KeyboardEnabled then
    getgenv().bypass_adonis = true
    loadstring(game:HttpGet('https://raw.githubusercontent.com/FakeAngles/PasteWare/refs/heads/main/PasteWareMobile.lua'))()
    return
end

if not game:IsLoaded() then
    game.Loaded:Wait()
end

if not syn or not protectgui then
    getgenv().protectgui = function() end
end

if getgenv().bypass_adonis then
    task.spawn(function()
        local g = getinfo or debug.getinfo
        local d = false
        local h = {}

        local x, y

        setthreadidentity(2)

        for i, v in getgc(true) do
            if typeof(v) == "table" then
                local a = rawget(v, "Detected")
                local b = rawget(v, "Kill")

                if typeof(a) == "function" and not x then
                    x = a
                    local o;
                    o = hookfunction(x, function(c, f, n)
                        if c ~= "_" then
                            if d then
                                warn(`Adonis AntiCheat flagged\nMethod: {c}\nInfo: {f}`)
                            end
                        end

                        return true
                    end)
                    table.insert(h, x)
                end

                if rawget(v, "Variables") and rawget(v, "Process") and typeof(b) == "function" and not y then
                    y = b
                    local o;
                    o = hookfunction(y, function(f)
                        if d then
                            warn(`Adonis AntiCheat tried to kill (fallback): {f}`)
                        end
                    end)
                    table.insert(h, y)
                end
            end
        end

        local o;
        o = hookfunction(getrenv().debug.info, newcclosure(function(...)
            local a, f = ...

            if x and a == x then
                if d then
                    warn(`zins | adonis bypassed`)
                end

                return coroutine.yield(coroutine.running())
            end

            return o(...)
        end))

        setthreadidentity(7)
    end)
end


-- Drawing objects for Silent Aim FOV
local fov_circle = Drawing.new("Circle")
fov_circle.Thickness = 1
fov_circle.NumSides = 100
fov_circle.Radius = 180
fov_circle.Filled = false
fov_circle.Visible = false
fov_circle.ZIndex = 999
fov_circle.Transparency = 1
fov_circle.Color = Color3.fromRGB(54, 57, 241)

-- Expected arguments for namecall methods (Silent Aim)
local ExpectedArguments = {
    ViewportPointToRay = {
        ArgCountRequired = 2,
        Args = {"number", "number"}
    },
    ScreenPointToRay = {
        ArgCountRequired = 2,
        Args = {"number", "number"}
    },
    Raycast = {
        ArgCountRequired = 3,
        Args = {"Instance", "Vector3", "Vector3", "RaycastParams"}
    },
    FindPartOnRay = {
        ArgCountRequired = 2,
        Args = {"Ray", "Instance", "boolean", "boolean"}
    },
    FindPartOnRayWithIgnoreList = {
        ArgCountRequired = 3,
        Args = {"Ray", "table", "boolean", "boolean"}
    },
    FindPartOnRayWithWhitelist = {
        ArgCountRequired = 3,
        Args = {"Ray", "table", "boolean", "boolean"}
    }
}

-- Utility functions
local function CalculateChance(Percentage)
    Percentage = math.floor(Percentage)
    local chance = math.floor(Random.new().NextNumber(Random.new(), 0, 1) * 100) / 100
    return chance <= Percentage / 100
end

local function getPositionOnScreen(Vector)
    local Vec3, OnScreen = Camera:WorldToScreenPoint(Vector)
    return Vector2.new(Vec3.X, Vec3.Y), OnScreen
end

local function ValidateArguments(Args, RayMethod)
    local Matches = 0
    if #Args < RayMethod.ArgCountRequired then
        return false
    end
    for Pos, Argument in next, Args do
        if typeof(Argument) == RayMethod.Args[Pos] then
            Matches = Matches + 1
        end
    end
    return Matches >= RayMethod.ArgCountRequired
end

local function getDirection(Origin, Position)
    return (Position - Origin).Unit * 1000
end

local function getMousePosition()
    return UserInputService:GetMouseLocation()
end

local function IsPlayerVisible(Player)
    local PlayerCharacter = Player.Character
    local LocalPlayerCharacter = LocalPlayer.Character

    if not (PlayerCharacter or LocalPlayerCharacter) then return end

    local PlayerRoot = PlayerCharacter and PlayerCharacter:FindFirstChild(getgenv().Settings.SilentAim.TargetPart) or PlayerCharacter:FindFirstChild("HumanoidRootPart")

    if not PlayerRoot then return end

    local CastPoints, IgnoreList = {PlayerRoot.Position, LocalPlayerCharacter, PlayerCharacter}, {LocalPlayerCharacter, PlayerCharacter}
    local ObscuringObjects = #Camera:GetPartsObscuringTarget(CastPoints, IgnoreList)

    return ((ObscuringObjects == 0 and true) or (ObscuringObjects > 0 and false))
end

local function getClosestPlayer()
    if not getgenv().Settings.SilentAim.TargetPart then return end
    local Closest
    local DistanceToMouse
    local ignoredPlayers = Options.PlayerDropdown.Value or {}

    for _, Player in next, Players:GetPlayers() do
        if Player == LocalPlayer then continue end
        if ignoredPlayers[Player.Name] then continue end
        if getgenv().Settings.SilentAim.TeamCheck and Player.Team == LocalPlayer.Team then continue end
        local Character = Player.Character
        if not Character then continue end
        local HumanoidRootPart = Character:FindFirstChild("HumanoidRootPart")
        local Humanoid = Character:FindFirstChildOfClass("Humanoid")
        if not HumanoidRootPart or not Humanoid or Humanoid.Health <= 0 then continue end
        local ScreenPosition, OnScreen = getPositionOnScreen(HumanoidRootPart.Position)
        if not OnScreen then continue end
        local Distance = (getMousePosition() - ScreenPosition).Magnitude
        if Distance <= (DistanceToMouse or getgenv().Settings.SilentAim.FOVRadius) then
            local targetPartName = getgenv().Settings.SilentAim.TargetPart
            if targetPartName == "Random" then
                targetPartName = {"Head", "HumanoidRootPart"}[math.random(1, 2)]
            end
            Closest = Character:FindFirstChild(targetPartName)
            DistanceToMouse = Distance
        end
    end
    return Closest
end

local isLockedOn = false
local targetPlayer = nil

local function getBodyPart(character, part)
    return character:FindFirstChild(part) and part or "Head"
end

local function getNearestPlayerToMouse()
    if not getgenv().Settings.AimLock.Enabled then return nil end
    local nearestPlayer = nil
    local shortestDistance = math.huge
    local mousePosition = Camera:ViewportPointToRay(Mouse.X, Mouse.Y).Origin

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(getgenv().Settings.AimLock.BodyPart) then
            local part = player.Character[getgenv().Settings.AimLock.BodyPart]
            local screenPosition, onScreen = Camera:WorldToViewportPoint(part.Position)
            if onScreen then
                local distance = (Vector2.new(screenPosition.X, screenPosition.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
                if distance < shortestDistance then
                    nearestPlayer = player
                    shortestDistance = distance
                end
            end
        end
    end
    return nearestPlayer
end

local function toggleAimLock()
    if not getgenv().Settings.AimLock.Enabled then return end

    if isLockedOn then
        isLockedOn = false
        targetPlayer = nil
    else
        targetPlayer = getNearestPlayerToMouse()
        if targetPlayer and targetPlayer.Character then
            local part = getBodyPart(targetPlayer.Character, getgenv().Settings.AimLock.BodyPart)
            if targetPlayer.Character:FindFirstChild(part) then
                isLockedOn = true
            end
        end
    end
end

-- Desync related variables
local originalAmbient, originalOutdoorAmbient = Lighting.Ambient, Lighting.OutdoorAmbient
local originalFogStart, originalFogEnd, originalFogColor = Lighting.FogStart, Lighting.FogEnd, Lighting.FogColor
local nebulaThemeColor = Color3.fromRGB(173, 216, 230)

RunService.RenderStepped:Connect(function()
    -- AimLock Logic
    if getgenv().Settings.AimLock.Enabled and isLockedOn and targetPlayer and targetPlayer.Character then
        local partName = getBodyPart(targetPlayer.Character, getgenv().Settings.AimLock.BodyPart)
        local part = targetPlayer.Character:FindFirstChild(partName)

        if part and targetPlayer.Character:FindFirstChildOfClass("Humanoid") and targetPlayer.Character:FindFirstChildOfClass("Humanoid").Health > 0 then
            local predictedPosition = part.Position + (part.AssemblyLinearVelocity * getgenv().Settings.AimLock.Prediction)

            if getgenv().Settings.AimLock.AntiLockEnabled then
                if getgenv().Settings.AimLock.ResolverMethod == "Recalculate" then
                    predictedPosition = predictedPosition + (part.AssemblyLinearVelocity * getgenv().Settings.AimLock.ResolverIntensity)
                elseif getgenv().Settings.AimLock.ResolverMethod == "Randomize" then
                    predictedPosition = predictedPosition + Vector3.new(
                        math.random() * getgenv().Settings.AimLock.ResolverIntensity - (getgenv().Settings.AimLock.ResolverIntensity / 2),
                        math.random() * getgenv().Settings.AimLock.ResolverIntensity - (getgenv().Settings.AimLock.ResolverIntensity / 2),
                        math.random() * getgenv().Settings.AimLock.ResolverIntensity - (getgenv().Settings.AimLock.ResolverIntensity / 2)
                    )
                elseif getgenv().Settings.AimLock.ResolverMethod == "Invert" then
                    predictedPosition = predictedPosition - (part.AssemblyLinearVelocity * getgenv().Settings.AimLock.ResolverIntensity * 2)
                end
            end

            local currentCameraPosition = Camera.CFrame.Position
            Camera.CFrame = CFrame.new(currentCameraPosition, predictedPosition) * CFrame.new(0, 0, getgenv().Settings.AimLock.Smoothing)
        else
            isLockedOn = false
            targetPlayer = nil
        end
    end

    -- FOV Circle and Silent Aim Target Visualization
    if getgenv().Settings.SilentAim.ShowSilentAimTarget then
        local closestPlayer = getClosestPlayer()
        if closestPlayer then
            local Root = closestPlayer.Parent.PrimaryPart or closestPlayer
            local RootToViewportPoint, IsOnScreen = Camera:WorldToViewportPoint(Root.Position)
            if getgenv().previousHighlight then
                getgenv().previousHighlight:Destroy()
                getgenv().previousHighlight = nil
            end
            if IsOnScreen then
                local highlight = closestPlayer.Parent:FindFirstChildOfClass("Highlight")
                if not highlight then
                    highlight = Instance.new("Highlight")
                    highlight.Parent = closestPlayer.Parent
                    highlight.Adornee = closestPlayer.Parent
                end
                highlight.FillColor = getgenv().Settings.SilentAim.MouseVisualizeColor or Color3.fromRGB(54, 57, 241)
                highlight.FillTransparency = 0.5
                highlight.OutlineColor = getgenv().Settings.SilentAim.MouseVisualizeColor or Color3.fromRGB(54, 57, 241)
                highlight.OutlineTransparency = 0
                getgenv().previousHighlight = highlight
            end
        else
            if getgenv().previousHighlight then
                getgenv().previousHighlight:Destroy()
                getgenv().previousHighlight = nil
            end
        end
    end

    if getgenv().Settings.SilentAim.FOVVisible then
        fov_circle.Visible = getgenv().Settings.SilentAim.FOVVisible
        fov_circle.Color = getgenv().Settings.SilentAim.FOVColor or Color3.fromRGB(54, 57, 241)
        fov_circle.Position = getMousePosition()
    end

    -- FOV Change
    if getgenv().Settings.World.FOV.Enabled then
        Camera.FieldOfView = getgenv().Settings.World.FOV.Value
    end

    -- Target Strafe
    if getgenv().Settings.TargetStrafe.Enabled and getgenv().Settings.TargetStrafe.Allowed then
        local targetPos = targetPlayer and targetPlayer.Position
        if targetPos then
            local angle = tick() * (getgenv().Settings.TargetStrafe.Speed / 10)
            local offset = getgenv().Settings.TargetStrafe.Mode == "Horizontal"
                and Vector3.new(math.cos(angle) * getgenv().Settings.TargetStrafe.Radius, 0, math.sin(angle) * getgenv().Settings.TargetStrafe.Radius)
                or Vector3.new(math.cos(angle) * getgenv().Settings.TargetStrafe.Radius, getgenv().Settings.TargetStrafe.Radius, math.sin(angle) * getgenv().Settings.TargetStrafe.Radius)
            LocalPlayer.Character:SetPrimaryPartCFrame(CFrame.new(targetPos + offset))
            LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(LocalPlayer.Character.HumanoidRootPart.Position, targetPos)
        else
            -- If target is lost, stop strafing
            getgenv().Settings.TargetStrafe.Enabled = false
            LocalPlayer.CameraMode = getgenv().Settings.TargetStrafe.OriginalCameraMode or Enum.CameraMode.Classic
            Camera.CameraSubject = LocalPlayer.Character.Humanoid
        end
    end
end)

RunService.Heartbeat:Connect(function()
    -- Desync heartbeat
    if getgenv().Settings.Desync.MasterEnabled then
        if getgenv().Settings.Desync.Enabled then
            local player = game.Players.LocalPlayer
            local character = player.Character
            if not character then return end

            local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
            if not humanoidRootPart then return end

            local originalVelocity = humanoidRootPart.Velocity

            local randomOffset = Vector3.new(
                math.random(-1, 1) * getgenv().Settings.Desync.Intensity * 1000,
                math.random(-1, 1) * getgenv().Settings.Desync.Intensity * 1000,
                math.random(-1, 1) * getgenv().Settings.Desync.Intensity * 1000
            )

            humanoidRootPart.Velocity = randomOffset
            humanoidRootPart.CFrame = humanoidRootPart.CFrame * CFrame.Angles(
                0,
                math.random(-1, 1) * getgenv().Settings.Desync.Intensity * 0.001,
                0
            )

            game:GetService("RunService").RenderStepped:Wait()

            humanoidRootPart.Velocity = originalVelocity
        end
    end

    -- RPG Spam heartbeat
    if getgenv().Settings.Exploits.RPGSpam.Enabled then
        wait(math.max(0.01, 1 / getgenv().Settings.Exploits.RPGSpam.Speed))
        local function getActiveWeaponForSpam()
            local validWeapons = {"RPG", "Javelin", "Stinger"}
            for _, weaponName in ipairs(validWeapons) do
                local weapon = workspace[LocalPlayer.Name]:FindFirstChild(weaponName)
                if weapon and weapon:IsA("Tool") and weapon.Parent == workspace[LocalPlayer.Name] then
                    return weaponName
                end
            end
            return nil
        end

        local activeWeapon = getActiveWeaponForSpam()
        if not activeWeapon then return end

        for i = 1, getgenv().Settings.Exploits.RPGSpam.Count do
            if not getgenv().Settings.Exploits.RPGSpam.Enabled then return end
            local targetHead = getClosestPlayer()
            if not targetHead then return end
            local targetPosition = targetHead.Position
            local directionToTarget = (targetPosition - LocalPlayer.Character.HumanoidRootPart.Position).unit

            local RocketSystem = game:GetService("ReplicatedStorage"):WaitForChild("RocketSystem")
            local FireRocket = RocketSystem:WaitForChild("Events"):WaitForChild("FireRocket")
            local FireRocketClient = RocketSystem:WaitForChild("Events"):WaitForChild("FireRocketClient")

            if getgenv().Settings.Exploits.RPGSpam.Mode == "Rocket" then
                FireRocket:InvokeServer(directionToTarget, workspace[LocalPlayer.Name][activeWeapon], workspace[LocalPlayer.Name][activeWeapon], targetPosition)
                FireRocketClient:Fire(
                    targetPosition,
                    directionToTarget,
                    {
                        ["expShake"] = {["fadeInTime"] = 0.05, ["magnitude"] = 3, ["rotInfluence"] = {0.4, 0, 0.4}, ["fadeOutTime"] = 0.5, ["posInfluence"] = {1, 1, 0}, ["roughness"] = 3},
                        ["gravity"] = Vector3.new(0, -20, 0),
                        ["HelicopterDamage"] = 450,
                        ["FireRate"] = 15,
                        ["VehicleDamage"] = 350,
                        ["ExpName"] = "RPG",
                        ["ExpRadius"] = 12,
                        ["BoatDamage"] = 300,
                        ["TankDamage"] = 300,
                        ["Acceleration"] = 8,
                        ["ShieldDamage"] = 170,
                        ["Distance"] = 4000,
                        ["PlaneDamage"] = 500,
                        ["GunshipDamage"] = 170,
                        ["velocity"] = 200,
                        ["ExplosionDamage"] = 120
                    },
                    RocketSystem.Rockets["RPG Rocket"],
                    workspace[LocalPlayer.Name][activeWeapon],
                    workspace[LocalPlayer.Name][activeWeapon],
                    LocalPlayer
                )
            elseif getgenv().Settings.Exploits.RPGSpam.Mode == "Explode" then
                FireRocket:InvokeServer(directionToTarget, workspace[LocalPlayer.Name][activeWeapon], workspace[LocalPlayer.Name][activeWeapon], targetPosition)
                local RS = game:GetService("ReplicatedStorage").RocketSystem.Events
                RS.RocketHit:FireServer(
                    targetPosition,
                    directionToTarget,
                    workspace[LocalPlayer.Name][activeWeapon],
                    workspace[LocalPlayer.Name][activeWeapon],
                    targetHead,
                    targetHead,
                    LocalPlayer.Name .. "Rocket" .. (getgenv().rocketNumber or 1)
                )
                getgenv().rocketNumber = (getgenv().rocketNumber or 1) + 1
            end
        end
    end
end)

-- Movement loop
local humanoid = nil
while true do
    task.wait()

    if getgenv().Settings.Movement.Enabled then
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")

            if getgenv().Settings.Movement.Speed.Enabled and humanoid and humanoid.MoveDirection.Magnitude > 0 then
                local moveDirection = humanoid.MoveDirection.Unit
                LocalPlayer.Character.HumanoidRootPart.CFrame = LocalPlayer.Character.HumanoidRootPart.CFrame + moveDirection * getgenv().Settings.Movement.Speed.Multiplier
            end

            if getgenv().Settings.Movement.Fly.Enabled then
                local flyDirection = Vector3.zero

                if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                    flyDirection = flyDirection + Camera.CFrame.LookVector
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                    flyDirection = flyDirection - Camera.CFrame.LookVector
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.A) then
                    flyDirection = flyDirection - Camera.CFrame.RightVector
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.D) then
                    flyDirection = flyDirection + Camera.CFrame.RightVector
                end

                if flyDirection.Magnitude > 0 then
                    flyDirection = flyDirection.Unit
                end

                local newPosition = LocalPlayer.Character.HumanoidRootPart.Position + flyDirection * getgenv().Settings.Movement.Fly.Speed
                LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(newPosition)
                LocalPlayer.Character.HumanoidRootPart.Velocity = Vector3.new(0, 0, 0)
            end

            if getgenv().Settings.Movement.NoClip.Enabled then
                for _, v in pairs(LocalPlayer.Character:GetDescendants()) do
                    if v:IsA("BasePart") and v.CanCollide then
                        v.CanCollide = false
                    end
                end
            end
        end
    end
end

-- Silent Aim namecall hook
local oldNamecall
oldNamecall = hookmetamethod(game, "__namecall", newcclosure(function(...)
    local Method, Arguments = getnamecallmethod(), {...}
    local self = Arguments[1]
    local chance = CalculateChance(getgenv().Settings.SilentAim.HitChance)

    local BlockedMethods = getgenv().Settings.SilentAim.BlockedMethods or {}
    if Method == "Destroy" and self == Client then
        return
    end
    if table.find(BlockedMethods, Method) then
        return
    end

    local CanContinue = false
    if getgenv().Settings.SilentAim.CheckForFireFunc and (Method == "FindPartOnRay" or Method == "FindPartOnRayWithWhitelist" or Method == "FindPartOnRayWithIgnoreList" or Method == "Raycast" or Method == "ViewportPointToRay" or Method == "ScreenPointToRay") then
        local Traceback = tostring(debug.traceback()):lower()
        if Traceback:find("bullet") or Traceback:find("gun") or Traceback:find("fire") then
            CanContinue = true
        else
            return oldNamecall(...)
        end
    end

    if getgenv().Settings.SilentAim.Enabled and self == workspace and not checkcaller() and chance then
        local HitPart = getClosestPlayer()
        if HitPart then
            local function modifyRay(Origin)
                if getgenv().Settings.SilentAim.BulletTP then
                    Origin = (HitPart.CFrame * CFrame.new(0, 0, 1)).p
                end
                return Origin, getDirection(Origin, HitPart.Position)
            end

            if Method == "FindPartOnRayWithIgnoreList" and getgenv().Settings.SilentAim.Method == Method then
                if ValidateArguments(Arguments, ExpectedArguments.FindPartOnRayWithIgnoreList) then
                    local Origin, Direction = modifyRay(Arguments[2].Origin)
                    Arguments[2] = Ray.new(Origin, Direction * getgenv().Settings.SilentAim.MultiplyUnitBy)
                    return oldNamecall(unpack(Arguments))
                end
            elseif Method == "FindPartOnRayWithWhitelist" and getgenv().Settings.SilentAim.Method == Method then
                if ValidateArguments(Arguments, ExpectedArguments.FindPartOnRayWithWhitelist) then
                    local Origin, Direction = modifyRay(Arguments[2].Origin)
                    Arguments[2] = Ray.new(Origin, Direction * getgenv().Settings.SilentAim.MultiplyUnitBy)
                    return oldNamecall(unpack(Arguments))
                end
            elseif (Method == "FindPartOnRay" or Method == "findPartOnRay") and getgenv().Settings.SilentAim.Method:lower() == Method:lower() then
                if ValidateArguments(Arguments, ExpectedArguments.FindPartOnRay) then
                    local Origin, Direction = modifyRay(Arguments[2].Origin)
                    Arguments[2] = Ray.new(Origin, Direction * getgenv().Settings.SilentAim.MultiplyUnitBy)
                    return oldNamecall(unpack(Arguments))
                end
            elseif Method == "Raycast" and getgenv().Settings.SilentAim.Method == Method then
                if ValidateArguments(Arguments, ExpectedArguments.Raycast) then
                    local Origin, Direction = modifyRay(Arguments[2])
                    Arguments[2], Arguments[3] = Origin, Direction * getgenv().Settings.SilentAim.MultiplyUnitBy
                    return oldNamecall(unpack(Arguments))
                end
            elseif Method == "ViewportPointToRay" and getgenv().Settings.SilentAim.Method == Method then
                if ValidateArguments(Arguments, ExpectedArguments.ViewportPointToRay) then
                    local Origin = Camera.CFrame.p
                    if getgenv().Settings.SilentAim.BulletTP then
                        Origin = (HitPart.CFrame * CFrame.new(0, 0, 1)).p
                    end
                    Arguments[2] = Camera:WorldToScreenPoint(HitPart.Position)
                    return Ray.new(Origin, (HitPart.Position - Origin).Unit * getgenv().Settings.SilentAim.MultiplyUnitBy)
                end
            elseif Method == "ScreenPointToRay" and getgenv().Settings.SilentAim.Method == Method then
                if ValidateArguments(Arguments, ExpectedArguments.ScreenPointToRay) then
                    local Origin = Camera.CFrame.p
                    if getgenv().Settings.SilentAim.BulletTP then
                        Origin = (HitPart.CFrame * CFrame.new(0, 0, 1)).p
                    end
                    Arguments[2] = Camera:WorldToScreenPoint(HitPart.Position)
                    return Ray.new(Origin, (HitPart.Position - Origin).Unit * getgenv().Settings.SilentAim.MultiplyUnitBy)
                end
            end
        end
    end

    return oldNamecall(...)
end))

-- World ClockTime hook
local oldNewIndex
oldNewIndex = hookmetamethod(game, "__newindex", function(self, property, value)
    if not checkcaller() and self == Lighting then
        if property == "ClockTime" then value = getgenv().Settings.World.ClockTime end
    end
    return oldNewIndex(self, property, value)
end)

-- Explosion/Rocket manipulation hooks (War Tycoon Exploits)
local bulletHookEnabled = false
local rocketHookEnabled = false
local oldBulletNamecall
local oldRocketNamecall

local function enableBulletHitManipulation(value)
    if not getgenv().Settings.Exploits.MasterToggle then return end
    getgenv().Settings.Exploits.BulletManipulation = value
    local remote = game:GetService("ReplicatedStorage").BulletFireSystem.BulletHit

    if getgenv().Settings.Exploits.BulletManipulation then
        if not bulletHookEnabled then
            bulletHookEnabled = true
            oldBulletNamecall = hookmetamethod(remote, "__namecall", newcclosure(function(self, ...)
                if typeof(self) == "Instance" then
                    local method = getnamecallmethod()
                    if method and (method == "FireServer" and self == remote) then
                        local HitPart = getClosestPlayer()
                        if HitPart then
                            local remArgs = {...}
                            remArgs[2] = HitPart
                            remArgs[3] = HitPart.Position
                            setnamecallmethod(method)
                            return oldBulletNamecall(self, unpack(remArgs))
                        else
                            setnamecallmethod(method)
                        end
                    end
                end
                return oldBulletNamecall(self, ...)
            end))
        end
    else
        if bulletHookEnabled then
            bulletHookEnabled = false
            if oldBulletNamecall then
                hookmetamethod(remote, "__namecall", oldBulletNamecall)
                oldBulletNamecall = nil
            end
        end
    end
end

local function enableRocketHitManipulation(value)
    if not getgenv().Settings.Exploits.MasterToggle then return end
    getgenv().Settings.Exploits.RocketManipulation = value
    local remote = game:GetService("ReplicatedStorage").RocketSystem.Events.RocketHit

    if getgenv().Settings.Exploits.RocketManipulation and not rocketHookEnabled then
        rocketHookEnabled = true
        oldRocketNamecall = hookmetamethod(remote, "__namecall", newcclosure(function(self, ...)
            if typeof(self) == "Instance" and getnamecallmethod() == "FireServer" and self == remote then
                local remArgs = {...}
                local targetPart = getClosestPlayer()
                if targetPart then
                    remArgs[1] = targetPart.Position
                    remArgs[2] = (targetPart.Position - game:GetService("Players").LocalPlayer.Character.HumanoidRootPart.Position).unit
                    remArgs[5] = targetPart
                    setnamecallmethod("FireServer")
                    return oldRocketNamecall(self, unpack(remArgs))
                end
            end
            return oldRocketNamecall(self, ...)
        end))
    elseif not getgenv().Settings.Exploits.RocketManipulation and rocketHookEnabled then
        rocketHookEnabled = false
        if oldRocketNamecall then
            hookmetamethod(remote, "__namecall", oldRocketNamecall)
            oldRocketNamecall = nil
        end
    end
end

-- Weapon settings modification
local function modifyWeaponSettings(property, value)
    local function findSettingsModule(parent)
        for _, child in pairs(parent:GetChildren()) do
            if child:IsA("ModuleScript") then
                local success, module = pcall(function() return require(child) end)
                if success and module[property] ~= nil then
                    return module
                end
            end
            local found = findSettingsModule(child)
            if found then
                return found
            end
        end
        return nil
    end

    local player = game:GetService("Players").LocalPlayer
    local backpack = player:WaitForChild("Backpack")
    local character = player.Character or player.CharacterAdded:Wait()
    local foundModules = {}

    local function findSettingsInWarTycoon(item)
        local weaponName = item.Name
        local settingsModule = game:GetService("ReplicatedStorage"):WaitForChild("Configurations"):WaitForChild("ACS_Guns"):FindFirstChild(weaponName)
        if settingsModule then
            return settingsModule:FindFirstChild("Settings")
        end
        return nil
    end

    if getgenv().Settings.Exploits.WarTycoon then
        if getgenv().Settings.Exploits.WeaponOnHands then
            local toolInHand = character:FindFirstChildOfClass("Tool")
            if toolInHand then
                local settingsModule = findSettingsInWarTycoon(toolInHand)
                if settingsModule then
                    local success, module = pcall(function() return require(settingsModule) end)
                    if success and module[property] ~= nil then
                        module[property] = value
                    end
                end
            end
        else
            for _, item in pairs(backpack:GetChildren()) do
                local settingsModule = findSettingsInWarTycoon(item)
                if settingsModule then
                    local success, module = pcall(function() return require(settingsModule) end)
                    if success and module[property] ~= nil then
                        module[property] = value
                    end
                end
            end
        end
    else
        if getgenv().Settings.Exploits.WeaponOnHands then
            local toolInHand = character:FindFirstChildOfClass("Tool")
            if toolInHand then
                local settingsModule = findSettingsModule(toolInHand)
                if settingsModule then
                    local success, module = pcall(function() return require(settingsModule) end)
                    if success and module[property] ~= nil then
                        module[property] = value
                    end
                end
            end
        else
            for _, item in pairs(backpack:GetChildren()) do
                local settingsModule = findSettingsModule(item)
                if settingsModule then
                    local success, module = pcall(function() return require(settingsModule) end)
                    if success and module[property] ~= nil then
                        module[property] = value
                    end
                end
            end
        end
    end
end

-- ESP System
local espBoxes, espTracers, espNameTags = {}, {}, {}

local function createESPBox(color)
    local box = Drawing.new("Square")
    box.Color, box.Thickness, box.Filled, box.Visible = color, 1, false, false
    return box
end

local function createTracer(color)
    local tracer = Drawing.new("Line")
    tracer.Color, tracer.Thickness, tracer.Visible = color, 2, false
    return tracer
end

local function createNameTag(color, text)
    local nameTag = Drawing.new("Text")
    nameTag.Color, nameTag.Text, nameTag.Size, nameTag.Center, nameTag.Outline, nameTag.OutlineColor, nameTag.Visible = color, text, 15, true, true, Color3.fromRGB(0, 0, 0), false
    return nameTag
end

local function smoothInterpolation(from, to, factor)
    return from + (to - from) * factor
end

local function updateESPVisuals()
    for player, box in pairs(espBoxes) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            if getgenv().Settings.ESP.TeamCheck and player.Team == Players.LocalPlayer.Team then
                box.Visible = false
            else
                local rootPart = player.Character.HumanoidRootPart
                local screenPosition, onScreen = Camera:WorldToViewportPoint(rootPart.Position)
                if onScreen and getgenv().Settings.ESP.Box.Enabled then
                    local distance = screenPosition.Z
                    local scaleFactor = 70 / distance
                    local boxWidth = 30 * scaleFactor
                    local boxHeight = 50 * scaleFactor
                    box.Size = Vector2.new(boxWidth, boxHeight)
                    box.Position = Vector2.new(screenPosition.X - boxWidth / 2, screenPosition.Y - boxHeight / 2)
                    box.Visible = true
                else
                    box.Visible = false
                end
            end
        else
            box.Visible = false
        end
    end

    for player, tracer in pairs(espTracers) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            if getgenv().Settings.ESP.TeamCheck and player.Team == Players.LocalPlayer.Team then
                tracer.Visible = false
            else
                local rootPart = player.Character.HumanoidRootPart
                local screenPosition, onScreen = Camera:WorldToViewportPoint(rootPart.Position)
                if onScreen and getgenv().Settings.ESP.Tracers.Enabled then
                    local screenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
                    local targetPosition = Vector2.new(screenPosition.X, screenPosition.Y)
                    tracer.From = smoothInterpolation(tracer.From, screenCenter, 0.1)
                    tracer.To = smoothInterpolation(tracer.To, targetPosition, 0.1)
                    tracer.Visible = true
                else
                    tracer.Visible = false
                end
            end
        else
            tracer.Visible = false
        end
    end

    for player, nameTag in pairs(espNameTags) do
        if player.Character and player.Character:FindFirstChild("Head") then
            if getgenv().Settings.ESP.TeamCheck and player.Team == Players.LocalPlayer.Team then
                nameTag.Visible = false
            else
                local headPosition, onScreen = Camera:WorldToViewportPoint(player.Character.Head.Position)
                if onScreen and getgenv().Settings.ESP.NameTags.Enabled then
                    nameTag.Position = Vector2.new(headPosition.X, headPosition.Y - 30)
                    nameTag.Visible = true
                else
                    nameTag.Visible = false
                end
            end
        else
            nameTag.Visible = false
        end
    end
end

local function addESP(player)
    if player ~= Players.LocalPlayer then
        local box = createESPBox(getgenv().Settings.ESP.Box.Color)
        espBoxes[player] = box
        player.CharacterAdded:Connect(function()
            espBoxes[player] = box
        end)
    end
end

local function addTracer(player)
    if player ~= Players.LocalPlayer then
        local tracer = createTracer(getgenv().Settings.ESP.Tracers.Color)
        espTracers[player] = tracer
        player.CharacterAdded:Connect(function()
            espTracers[player] = tracer
        end)
    end
end

local function addNameTag(player)
    if player ~= Players.LocalPlayer then
        local nameTag = createNameTag(getgenv().Settings.ESP.NameTags.Color, player.Name)
        espNameTags[player] = nameTag
        player.CharacterAdded:Connect(function()
            espNameTags[player] = nameTag
        end)
    end
end

local function removeESP(player)
    if espBoxes[player] then
        espBoxes[player].Visible = false
        espBoxes[player] = nil
    end
end

local function removeTracer(player)
    if espTracers[player] then
        espTracers[player].Visible = false
        espTracers[player] = nil
    end
end

local function removeNameTag(player)
    if espNameTags[player] then
        espNameTags[player].Visible = false
        espNameTags[player] = nil
    end
end

local function updateTeamColor(player)
    local teamColor = player.Team and player.Team.TeamColor.Color
    if getgenv().Settings.ESP.TeamColor then
        if espBoxes[player] then espBoxes[player].Color = teamColor end
        if espTracers[player] then espTracers[player].Color = teamColor end
        if espNameTags[player] then espNameTags[player].Color = teamColor end
    else
        if espBoxes[player] then espBoxes[player].Color = getgenv().Settings.ESP.Box.Color end
        if espTracers[player] then espTracers[player].Color = getgenv().Settings.ESP.Tracers.Color end
        if espNameTags[player] then espNameTags[player].Color = getgenv().Settings.ESP.NameTags.Color end
    end
end

Players.PlayerAdded:Connect(function(player)
    addESP(player)
    addTracer(player)
    addNameTag(player)
    player:GetPropertyChangedSignal("Team"):Connect(function() updateTeamColor(player) end)
    updateTeamColor(player)
end)

Players.PlayerRemoving:Connect(function(player)
    removeESP(player)
    removeTracer(player)
    removeNameTag(player)
end)

for _, player in pairs(Players:GetPlayers()) do
    addESP(player)
    addTracer(player)
    addNameTag(player)
end

RunService.RenderStepped:Connect(updateESPVisuals)

-- Skybox System
local Skyboxes = {}

local Visuals = {}
function Visuals:NewSky(Data)
    local Name = Data.Name
    Skyboxes[Name] = {
        SkyboxBk = Data.SkyboxBk,
        SkyboxDn = Data.SkyboxDn,
        SkyboxFt = Data.SkyboxFt,
        SkyboxLf = Data.SkyboxLf,
        SkyboxRt = Data.SkyboxRt,
        SkyboxUp = Data.SkyboxUp,
        MoonTextureId = Data.Moon or "rbxasset://sky/moon.jpg",
        SunTextureId = Data.Sun or "rbxasset://sky/sun.jpg"
    }
end

function Visuals:SwitchSkybox(Name)
    local OldSky = Lighting:FindFirstChildOfClass("Sky")
    if OldSky then OldSky:Destroy() end

    local Sky = Instance.new("Sky", Lighting)
    for Index, Value in pairs(Skyboxes[Name]) do
        Sky[Index] = Value
    end
end

if Lighting:FindFirstChildOfClass("Sky") then
    local OldSky = Lighting:FindFirstChildOfClass("Sky")
    Visuals:NewSky({
        Name = "Game's Default Sky",
        SkyboxBk = OldSky.SkyboxBk,
        SkyboxDn = OldSky.SkyboxDn,
        SkyboxFt = OldSky.SkyboxFt,
        SkyboxLf = OldSky.SkyboxLf,
        SkyboxRt = OldSky.SkyboxRt,
        SkyboxUp = OldSky.SkyboxUp
    })
end

Visuals:NewSky({
    Name = "Sunset",
    SkyboxBk = "rbxassetid://600830446",
    SkyboxDn = "rbxassetid://600831635",
    SkyboxFt = "rbxassetid://600832720",
    SkyboxLf = "rbxassetid://600886090",
    SkyboxRt = "rbxassetid://600833862",
    SkyboxUp = "rbxassetid://600835177"
})

Visuals:NewSky({
    Name = "Arctic",
    SkyboxBk = "http://www.roblox.com/asset/?id=225469390",
    SkyboxDn = "http://www.roblox.com/asset/?id=225469395",
    SkyboxFt = "http://www.roblox.com/asset/?id=225469403",
    SkyboxLf = "http://www.roblox.com/asset/?id=225469450",
    SkyboxRt = "http://www.roblox.com/asset/?id=225469471",
    SkyboxUp = "http://www.roblox.com/asset/?id=225469481"
})

Visuals:NewSky({
    Name = "Space",
    SkyboxBk = "http://www.roblox.com/asset/?id=166509999",
    SkyboxDn = "http://www.roblox.com/asset/?id=166510057",
    SkyboxFt = "http://www.roblox.com/asset/?id=166510116",
    SkyboxLf = "http://www.roblox.com/asset/?id=166510092",
    SkyboxRt = "http://www.roblox.com/asset/?id=166510131",
    SkyboxUp = "http://www.roblox.com/asset/?id=166510114"
})

Visuals:NewSky({
    Name = "Roblox Default",
    SkyboxBk = "rbxasset://textures/sky/sky512_bk.tex",
    SkyboxDn = "rbxasset://textures/sky/sky512_dn.tex",
    SkyboxFt = "rbxasset://textures/sky/sky512_ft.tex",
    SkyboxLf = "rbxasset://textures/sky/sky512_lf.tex",
    SkyboxRt = "rbxasset://textures/sky/sky512_rt.tex",
    SkyboxUp = "rbxasset://textures/sky/sky512_up.tex"
})

Visuals:NewSky({
    Name = "Red Night",
    SkyboxBk = "http://www.roblox.com/Asset/?ID=401664839";
    SkyboxDn = "http://www.roblox.com/Asset/?ID=401664862";
    SkyboxFt = "http://www.roblox.com/Asset/?ID=401664960";
    SkyboxLf = "http://www.roblox.com/Asset/?ID=401664881";
    SkyboxRt = "http://www.roblox.com/Asset/?ID=401664901";
    SkyboxUp = "http://www.roblox.com/Asset/?ID=401664936";
})

Visuals:NewSky({
    Name = "Deep Space",
    SkyboxBk = "http://www.roblox.com/asset/?id=149397692";
    SkyboxDn = "http://www.roblox.com/asset/?id=149397686";
    SkyboxFt = "http://www.roblox.com/asset/?id=149397697";
    SkyboxLf = "http://www.roblox.com/asset/?id=149397684";
    SkyboxRt = "http://www.roblox.com/asset/?id=149397688";
    SkyboxUp = "http://www.roblox.com/asset/?id=149397702";
})

Visuals:NewSky({
    Name = "Pink Skies",
    SkyboxBk = "http://www.roblox.com/asset/?id=151165214";
    SkyboxDn = "http://www.roblox.com/asset/?id=151165197";
    SkyboxFt = "http://www.roblox.com/asset/?id=151165224";
    SkyboxLf = "http://www.roblox.com/asset/?id=151165191";
    SkyboxRt = "http://www.roblox.com/asset/?id=151165206";
    SkyboxUp = "http://www.roblox.com/asset/?id=151165227";
})

Visuals:NewSky({
    Name = "Purple Sunset",
    SkyboxBk = "rbxassetid://264908339";
    SkyboxDn = "rbxassetid://264907909";
    SkyboxFt = "rbxassetid://264909420";
    SkyboxLf = "rbxassetid://264909758";
    SkyboxRt = "rbxassetid://264908886";
    SkyboxUp = "rbxassetid://264907379";
})

Visuals:NewSky({
    Name = "Blue Night",
    SkyboxBk = "http://www.roblox.com/Asset/?ID=12064107";
    SkyboxDn = "http://www.roblox.com/Asset/?ID=12064152";
    SkyboxFt = "http://www.roblox.com/Asset/?ID=12064121";
    SkyboxLf = "http://www.roblox.com/Asset/?ID=12063984";
    SkyboxRt = "http://www.roblox.com/Asset/?ID=12064115";
    SkyboxUp = "http://www.roblox.com/Asset/?ID=12064131";
})

Visuals:NewSky({
    Name = "Blossom Daylight",
    SkyboxBk = "http://www.roblox.com/asset/?id=271042516";
    SkyboxDn = "http://www.roblox.com/asset/?id=271077243";
    SkyboxFt = "http://www.roblox.com/asset/?id=271042556";
    SkyboxLf = "http://www.roblox.com/asset/?id=271042310";
    SkyboxRt = "http://www.roblox.com/asset/?id=271042467";
    SkyboxUp = "http://www.roblox.com/asset/?id=271077958";
})

Visuals:NewSky({
    Name = "Blue Nebula",
    SkyboxBk = "http://www.roblox.com/asset?id=135207744";
    SkyboxDn = "http://www.roblox.com/asset?id=135207662";
    SkyboxFt = "http://www.roblox.com/asset?id=135207770";
    SkyboxLf = "http://www.roblox.com/asset?id=135207615";
    SkyboxRt = "http://www.roblox.com/asset?id=135207695";
    SkyboxUp = "http://www.roblox.com/asset?id=135207794";
})

Visuals:NewSky({
    Name = "Blue Planet",
    SkyboxBk = "rbxassetid://218955819";
    SkyboxDn = "rbxassetid://218953419";
    SkyboxFt = "rbxassetid://218954524";
    SkyboxLf = "rbxassetid://218958493";
    SkyboxRt = "rbxassetid://218957134";
    SkyboxUp = "rbxassetid://218950090";
})

Visuals:NewSky({
    Name = "Deep Space",
    SkyboxBk = "http://www.roblox.com/asset/?id=159248188";
    SkyboxDn = "http://www.roblox.com/asset/?id=159248183";
    SkyboxFt = "http://www.roblox.com/asset/?id=159248187";
    SkyboxLf = "http://www.roblox.com/asset/?id=159248173";
    SkyboxRt = "http://www.roblox.com/asset/?id=159248192";
    SkyboxUp = "http://www.roblox.com/asset/?id=159248176";
})

local SkyboxNames = {}
for Name, _ in pairs(Skyboxes) do
    table.insert(SkyboxNames, Name)
end


-- UI Library Initialization
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/FakeAngles/PasteWare/refs/heads/main/linoralib.lua"))()
local ThemeManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/FakeAngles/PasteWare/refs/heads/main/manage2.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/FakeAngles/PasteWare/refs/heads/main/manager.lua"))()

Library.KeybindFrame.Visible = true

local Window = Library:CreateWindow({
    Title = T("WindowTitle"),
    Center = true,
    AutoShow = true,
    TabPadding = 8,
    MenuFadeTime = 0.2
})

local GeneralTab = Window:AddTab(T("MainTab"))
local ExploitTab = Window:AddTab(T("ExploitsTab"))
local VisualsTab = Window:AddTab(T("VisualsTab"))
local SettingsTab = Window:AddTab(T("SettingsTab"))

ThemeManager:SetLibrary(Library)
SaveManager:SetLibrary(Library)
ThemeManager:ApplyToTab(SettingsTab)
SaveManager:BuildConfigSection(SettingsTab)

-- Language Toggle Button
local LanguageButton = SettingsTab:AddButton("Language", {
    Text = "English / Español",
    Callback = function()
        currentLanguage = (currentLanguage == "English" and "Spanish" or "English")
        Window:SetTitle(T("WindowTitle"))
        -- Rebuild UI elements to apply new translations
        -- This is a simplified approach; for complex UIs, a full rebuild or
        -- dynamic text updates on existing elements would be needed.
        -- For this example, we'll just update the window title and assume other elements
        -- will be recreated or updated through their callbacks if the user interacts with them.
        warn("Language changed to: " .. currentLanguage)
        -- You might need to re-create or update all UI elements here for a full translation refresh.
        -- For simplicity, we'll just show how to do it for a few elements.
        -- A more robust solution would involve storing references to all UI elements and iterating
        -- through them to update their text properties.
        Window:Close() -- Close and reopen to force a redraw with new language
        Window:Open()
        -- In a real scenario, you'd likely update the text of existing UI elements directly
        -- rather than closing and reopening the entire window, which might reset user selections.
    end
})

-- Update UI with current language
local function updateUI()
    Window:SetTitle(T("WindowTitle"))
    GeneralTab:SetTitle(T("MainTab"))
    ExploitTab:SetTitle(T("ExploitsTab"))
    VisualsTab:SetTitle(T("VisualsTab"))
    SettingsTab:SetTitle(T("SettingsTab"))

    -- --- Main Tab ---
    -- AimLock settings
    local aimbox = GeneralTab:AddRightGroupbox(T("AimLockGroupBox"))
    aimbox:AddToggle("AimLock_Enabled", {
        Text = T("EnableAimLock"),
        Default = getgenv().Settings.AimLock.Enabled,
        Tooltip = T("ToggleAimLockTooltip"),
        Callback = function(value)
            getgenv().Settings.AimLock.Enabled = value
            if not getgenv().Settings.AimLock.Enabled then
                isLockedOn = false
                targetPlayer = nil
            end
        end
    })

    aimbox:AddToggle("AimLock_Keybind", {
        Text = T("AimLockKeybind"),
        Default = false, -- This toggle itself is just for the keypicker
        Tooltip = T("ToggleAimLockTooltip"),
        Callback = function(value)
            -- This toggle doesn't directly control aimlock enabled state. It's just for the keypicker sync.
        end,
    }):AddKeyPicker("AimLock_Key", {
        Default = getgenv().Settings.AimLock.Keybind,
        SyncToggleState = true,
        Mode = "Toggle",
        Text = T("AimLockKey"),
        Tooltip = T("ToggleAimLockTooltip"),
        Callback = function()
            toggleAimLock()
        end,
    })

    aimbox:AddSlider("AimLock_Smoothing", {
        Text = T("CameraSmoothing"),
        Default = getgenv().Settings.AimLock.Smoothing,
        Min = 0, Max = 1, Rounding = 2,
        Tooltip = T("AdjustSmoothingTooltip"),
        Callback = function(value)
            getgenv().Settings.AimLock.Smoothing = value
        end,
    })

    aimbox:AddSlider("AimLock_Prediction", {
        Text = T("PredictionFactor"),
        Default = getgenv().Settings.AimLock.Prediction,
        Min = 0, Max = 2, Rounding = 2,
        Tooltip = T("AdjustPredictionTooltip"),
        Callback = function(value)
            getgenv().Settings.AimLock.Prediction = value
        end,
    })

    aimbox:AddDropdown("AimLock_BodyPart", {
        Values = T("BodyPartsValues"),
        Default = getgenv().Settings.AimLock.BodyPart,
        Multi = false,
        Text = T("TargetBodyPart"),
        Tooltip = T("SelectBodyPartTooltip"),
        Callback = function(value)
            getgenv().Settings.AimLock.BodyPart = value
        end,
    })

    -- Anti Lock
    local velbox = GeneralTab:AddRightGroupbox(T("AntiLockGroupBox"))
    velbox:AddToggle("Desync_MasterEnabled", {
        Text = T("EnableDesyncMaster"),
        Default = getgenv().Settings.Desync.MasterEnabled,
        Tooltip = T("EnableDesyncSystemTooltip"),
        Callback = function(value)
            getgenv().Settings.Desync.MasterEnabled = value
        end
    })

    velbox:AddToggle("Desync_Enabled", {
        Text = T("EnableDesync"),
        Default = false,
        Tooltip = T("EnableDesyncTooltip"),
        Callback = function(value)
            getgenv().Settings.Desync.Enabled = value
        end
    }):AddKeyPicker("Desync_Keybind", {
        Default = getgenv().Settings.Desync.Keybind,
        SyncToggleState = true,
        Mode = "Toggle",
        Text = T("DesyncToggleKey"),
        Tooltip = T("ToggleDesyncKeyTooltip"),
        Callback = function(value)
            getgenv().Settings.Desync.Enabled = value
        end
    })

    velbox:AddSlider("Desync_Intensity", {
        Text = T("VelocityIntensity"),
        Default = getgenv().Settings.Desync.Intensity,
        Min = 1, Max = 10, Rounding = 0,
        Tooltip = T("AdjustVelocityIntensityTooltip"),
        Callback = function(value)
            getgenv().Settings.Desync.Intensity = value
        end
    })

    velbox:AddToggle("AimLock_AntiLockEnabled", {
        Text = T("EnableAntiLockResolver"),
        Default = getgenv().Settings.AimLock.AntiLockEnabled,
        Tooltip = T("EnableAntiLockTooltip"),
        Callback = function(value)
            getgenv().Settings.AimLock.AntiLockEnabled = value
        end,
    })

    velbox:AddSlider("AimLock_ResolverIntensity", {
        Text = T("ResolverIntensity"),
        Default = getgenv().Settings.AimLock.ResolverIntensity,
        Min = 0, Max = 5, Rounding = 2,
        Tooltip = T("AdjustResolverIntensityTooltip"),
        Callback = function(value)
            getgenv().Settings.AimLock.ResolverIntensity = value
        end,
    })

    velbox:AddDropdown("AimLock_ResolverMethod", {
        Values = T("ResolverMethodsValues"),
        Default = getgenv().Settings.AimLock.ResolverMethod,
        Multi = false,
        Text = T("ResolverMethod"),
        Tooltip = T("SelectResolverMethodTooltip"),
        Callback = function(value)
            getgenv().Settings.AimLock.ResolverMethod = value
        end,
    })

    -- Movement
    local frabox = GeneralTab:AddRightGroupbox(T("MovementGroupBox"))
    frabox:AddToggle("Movement_Enabled", {
        Text = T("EnableMovement"),
        Default = getgenv().Settings.Movement.Enabled,
        Tooltip = T("EnableMovementToggleTooltip"),
        Callback = function(value)
            getgenv().Settings.Movement.Enabled = value
        end
    })

    frabox:AddToggle("Movement_Speed_Enabled", {
        Text = T("SpeedToggle"),
        Default = getgenv().Settings.Movement.Speed.Enabled,
        Tooltip = T("SpeedToggleTooltip"),
        Callback = function(value)
            getgenv().Settings.Movement.Speed.Enabled = value
        end
    }):AddKeyPicker("Movement_Speed_Keybind", {
        Default = getgenv().Settings.Movement.Speed.Keybind,
        SyncToggleState = true,
        Mode = "Toggle",
        Text = T("SpeedToggleKey"),
        Tooltip = T("SpeedKeybindTooltip"),
        Callback = function(value)
            getgenv().Settings.Movement.Speed.Enabled = value
        end
    })

    frabox:AddSlider("Movement_Speed_Multiplier", {
        Text = T("CFrameMultiplier"),
        Default = getgenv().Settings.Movement.Speed.Multiplier,
        Min = 1, Max = 20, Rounding = 1,
        Tooltip = T("CFrameMultiplierTooltip"),
        Callback = function(value)
            getgenv().Settings.Movement.Speed.Multiplier = value
        end,
    })

    frabox:AddToggle("Movement_Fly_Enabled", {
        Text = T("CFlyToggle"),
        Default = getgenv().Settings.Movement.Fly.Enabled,
        Tooltip = T("FlyToggleTooltip"),
        Callback = function(value)
            getgenv().Settings.Movement.Fly.Enabled = value
        end
    }):AddKeyPicker("Movement_Fly_Keybind", {
        Default = getgenv().Settings.Movement.Fly.Keybind,
        SyncToggleState = true,
        Mode = "Toggle",
        Text = T("CFlyToggleKey"),
        Tooltip = T("FlyKeybindTooltip"),
        Callback = function(value)
            getgenv().Settings.Movement.Fly.Enabled = value
        end
    })

    frabox:AddSlider("Movement_Fly_Speed", {
        Text = T("CFlySpeed"),
        Default = getgenv().Settings.Movement.Fly.Speed,
        Min = 1, Max = 50, Rounding = 1,
        Tooltip = T("FlySpeedTooltip"),
        Callback = function(value)
            getgenv().Settings.Movement.Fly.Speed = value
        end,
    })

    frabox:AddToggle("Movement_NoClip_Enabled", {
        Text = T("NoClipToggle"),
        Default = getgenv().Settings.Movement.NoClip.Enabled,
        Tooltip = T("NoClipToggleTooltip"),
        Callback = function(value)
            getgenv().Settings.Movement.NoClip.Enabled = value
        end
    }):AddKeyPicker("Movement_NoClip_Keybind", {
        Default = getgenv().Settings.Movement.NoClip.Keybind,
        SyncToggleState = true,
        Mode = "Toggle",
        Text = T("NoClipToggleKey"),
        Tooltip = T("NoClipKeybindTooltip"),
        Callback = function(value)
            getgenv().Settings.Movement.NoClip.Enabled = value
        end
    })

    -- Silent Aim
    local MainBOX = GeneralTab:AddLeftTabbox(T("SilentAimTabbox"))
    local Main = MainBOX:AddTab(T("SilentAimMainTab"))

    Main:AddToggle("SilentAim_Enabled", {
        Text = T("EnableSilentAim"),
        Default = getgenv().Settings.SilentAim.Enabled,
        Tooltip = T("SilentAimEnabledTooltip"),
    }):AddKeyPicker("SilentAim_Keybind", {
        Default = getgenv().Settings.SilentAim.ToggleKey,
        SyncToggleState = true,
        Mode = "Toggle",
        Text = T("SilentAimToggleKey"),
        Tooltip = T("SilentAimKeybindTooltip"),
        Callback = function()
            getgenv().Settings.SilentAim.Enabled = not getgenv().Settings.SilentAim.Enabled
        end,
    })

    Main:AddToggle("SilentAim_TeamCheck", {
        Text = T("TeamCheck"),
        Default = getgenv().Settings.SilentAim.TeamCheck,
        Tooltip = T("TeamCheckTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.TeamCheck = value
        end,
    })

    Main:AddToggle("SilentAim_BulletTP", {
        Text = T("BulletTeleport"),
        Default = getgenv().Settings.SilentAim.BulletTP,
        Tooltip = T("BulletTPTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.BulletTP = value
        end,
    })

    Main:AddToggle("SilentAim_CheckForFireFunc", {
        Text = T("CheckForFireFunction"),
        Default = getgenv().Settings.SilentAim.CheckForFireFunc,
        Tooltip = T("CheckFireFuncTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.CheckForFireFunc = value
        end,
    })

    Main:AddDropdown("SilentAim_TargetPart", {
        AllowNull = true,
        Text = T("TargetPart"),
        Default = getgenv().Settings.SilentAim.TargetPart,
        Values = {"Head", "HumanoidRootPart", "Random"},
        Tooltip = T("TargetPartTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.TargetPart = value
        end,
    })

    Main:AddDropdown("SilentAim_Method", {
        AllowNull = true,
        Text = T("SilentAimMethod"),
        Default = getgenv().Settings.SilentAim.Method,
        Values = {
            "ViewportPointToRay",
            "ScreenPointToRay",
            "Raycast",
            "FindPartOnRay",
            "FindPartOnRayWithIgnoreList"
        },
        Tooltip = T("SilentAimMethodTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.Method = value
        end,
    })

    Main:AddDropdown("SilentAim_BlockedMethods", {
        AllowNull = true,
        Multi = true,
        Text = T("BlockedMethods"),
        Default = getgenv().Settings.SilentAim.BlockedMethods,
        Values = T("BlockedMethodsValues"),
        Tooltip = T("BlockedMethodsTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.BlockedMethods = value
        end,
    })

    Main:AddDropdown("SilentAim_Include", {
        AllowNull = true,
        Multi = true,
        Text = T("Include"),
        Default = getgenv().Settings.SilentAim.Include,
        Values = T("IncludeValues"),
        Tooltip = T("IncludeTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.Include = value
        end,
    })

    Main:AddDropdown("SilentAim_Origin", {
        AllowNull = true,
        Multi = true,
        Text = T("Origin"),
        Default = getgenv().Settings.SilentAim.Origin,
        Values = T("OriginValues"),
        Tooltip = T("OriginTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.Origin = value
        end,
    })

    Main:AddSlider("SilentAim_MultiplyUnitBy", {
        Text = T("MultiplyUnitBy"),
        Default = getgenv().Settings.SilentAim.MultiplyUnitBy,
        Min = 0.1, Max = 10, Rounding = 1, Compact = false,
        Tooltip = T("MultiplyUnitByTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.MultiplyUnitBy = value
        end,
    })

    Main:AddSlider("SilentAim_HitChance", {
        Text = T("HitChance"),
        Default = getgenv().Settings.SilentAim.HitChance,
        Min = 0, Max = 100, Rounding = 1, Compact = false,
        Tooltip = T("HitChanceTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.HitChance = value
        end,
    })

    -- Field Of View
    local FieldOfViewBOX = GeneralTab:AddLeftTabbox(T("FOVTabbox"))
    local FOVMain = FieldOfViewBOX:AddTab(T("FOVVisualsTab"))

    FOVMain:AddToggle("SilentAim_FOVVisible", {
        Text = T("ShowFOVCircle"),
        Default = getgenv().Settings.SilentAim.FOVVisible,
        Tooltip = T("ShowFOVCircleTooltip"),
    }):AddColorPicker("SilentAim_FOVColor", {
        Default = Color3.fromRGB(54, 57, 241),
        Callback = function(color)
            getgenv().Settings.SilentAim.FOVColor = color
            fov_circle.Color = color
        end
    }):OnChanged(function()
        fov_circle.Visible = getgenv().Settings.SilentAim.FOVVisible
    end)


    FOVMain:AddSlider("SilentAim_FOVRadius", {
        Text = T("FOVRadius"),
        Min = 0, Max = 360, Default = getgenv().Settings.SilentAim.FOVRadius, Rounding = 0,
        Tooltip = T("FOVRadiusTooltip"),
        Callback = function(value)
            getgenv().Settings.SilentAim.FOVRadius = value
            fov_circle.Radius = value
        end,
    })

    FOVMain:AddToggle("SilentAim_ShowSilentAimTarget", {
        Text = T("ShowSilentAimTarget"),
        Default = getgenv().Settings.SilentAim.ShowSilentAimTarget,
        Tooltip = T("ShowSilentAimTargetTooltip"),
    }):AddColorPicker("SilentAim_MouseVisualizeColor", {
        Default = Color3.fromRGB(54, 57, 241),
        Callback = function(color)
            getgenv().Settings.SilentAim.MouseVisualizeColor = color
        end
    })

    FOVMain:AddDropdown("SilentAim_PlayerDropdown", {
        SpecialType = "Player",
        Text = T("IgnorePlayer"),
        Tooltip = T("IgnorePlayerTooltip"),
        Multi = true,
    })

    -- Target Strafe
    local targetStrafe = GeneralTab:AddLeftGroupbox(T("TargetStrafeGroupBox"))
    targetStrafe:AddToggle("TargetStrafe_Allowed", {
        Text = T("StrafeControlToggle"),
        Default = getgenv().Settings.TargetStrafe.Allowed,
        Tooltip = T("TargetStrafeAllowedTooltip"),
        Callback = function(value)
            getgenv().Settings.TargetStrafe.Allowed = value
            if not getgenv().Settings.TargetStrafe.Allowed and getgenv().Settings.TargetStrafe.Enabled then
                getgenv().Settings.TargetStrafe.Enabled = false
                LocalPlayer.CameraMode = getgenv().Settings.TargetStrafe.OriginalCameraMode or Enum.CameraMode.Classic
                Camera.CameraSubject = LocalPlayer.Character.Humanoid
            end
        end
    })

    targetStrafe:AddToggle("TargetStrafe_Enabled", {
        Text = T("EnableTargetStrafe"),
        Default = getgenv().Settings.TargetStrafe.Enabled,
        Tooltip = T("TargetStrafeToggleTooltip"),
        Callback = function(value)
            if getgenv().Settings.TargetStrafe.Allowed then
                getgenv().Settings.TargetStrafe.Enabled = value
                if getgenv().Settings.TargetStrafe.Enabled then
                    targetPlayer = getClosestPlayer() -- Re-find target when enabling
                    if targetPlayer and targetPlayer.Parent then
                        getgenv().Settings.TargetStrafe.OriginalCameraMode = LocalPlayer.CameraMode
                        LocalPlayer.CameraMode = Enum.CameraMode.Classic
                        Camera.CameraSubject = targetPlayer.Parent:FindFirstChild("Humanoid")
                    else
                        -- If no target found, disable strafe
                        getgenv().Settings.TargetStrafe.Enabled = false
                    end
                else
                    LocalPlayer.CameraMode = getgenv().Settings.TargetStrafe.OriginalCameraMode or Enum.CameraMode.Classic
                    Camera.CameraSubject = LocalPlayer.Character.Humanoid
                    targetPlayer = nil
                end
            end
        end
    }):AddKeyPicker("TargetStrafe_Keybind", {
        Default = getgenv().Settings.TargetStrafe.Keybind,
        SyncToggleState = true,
        Mode = "Toggle",
        Text = T("TargetStrafeToggleKey"),
        Tooltip = T("TargetStrafeKeybindTooltip"),
        Callback = function(value)
            if getgenv().Settings.TargetStrafe.Allowed then
                getgenv().Settings.TargetStrafe.Enabled = value
                if getgenv().Settings.TargetStrafe.Enabled then
                    targetPlayer = getClosestPlayer()
                    if targetPlayer and targetPlayer.Parent then
                        getgenv().Settings.TargetStrafe.OriginalCameraMode = LocalPlayer.CameraMode
                        LocalPlayer.CameraMode = Enum.CameraMode.Classic
                        Camera.CameraSubject = targetPlayer.Parent:FindFirstChild("Humanoid")
                    else
                        getgenv().Settings.TargetStrafe.Enabled = false
                    end
                else
                    LocalPlayer.CameraMode = getgenv().Settings.TargetStrafe.OriginalCameraMode or Enum.CameraMode.Classic
                    Camera.CameraSubject = LocalPlayer.Character.Humanoid
                    targetPlayer = nil
                end
            end
        end
    })

    targetStrafe:AddDropdown("TargetStrafe_Mode", {
        AllowNull = false,
        Text = T("TargetStrafeMode"),
        Default = getgenv().Settings.TargetStrafe.Mode,
        Values = T("StrafeModeValues"),
        Tooltip = T("TargetStrafeModeTooltip"),
        Callback = function(value)
            getgenv().Settings.TargetStrafe.Mode = value
        end
    })

    targetStrafe:AddSlider("TargetStrafe_Radius", {
        Text = T("StrafeRadius"),
        Default = getgenv().Settings.TargetStrafe.Radius,
        Min = 1, Max = 20, Rounding = 1,
        Tooltip = T("StrafeRadiusTooltip"),
        Callback = function(value)
            getgenv().Settings.TargetStrafe.Radius = value
        end
    })

    targetStrafe:AddSlider("TargetStrafe_Speed", {
        Text = T("StrafeSpeed"),
        Default = getgenv().Settings.TargetStrafe.Speed,
        Min = 10, Max = 200, Rounding = 1,
        Tooltip = T("StrafeSpeedTooltip"),
        Callback = function(value)
            getgenv().Settings.TargetStrafe.Speed = value
        end
    })


    -- --- Exploits Tab ---
    local WarTycoonBox = ExploitTab:AddLeftGroupbox(T("WarTycoonGroupBox"))
    WarTycoonBox:AddToggle("Exploits_MasterToggle", {
        Text = T("ExploitsMasterToggle"),
        Default = getgenv().Settings.Exploits.MasterToggle,
        Tooltip = "Enable or disable all exploit features globally.",
        Callback = function(value)
            getgenv().Settings.Exploits.MasterToggle = value
        end
    })

    WarTycoonBox:AddToggle("Exploits_BulletManipulation", {
        Text = T("BulletHitManipulation"),
        Default = getgenv().Settings.Exploits.BulletManipulation,
        Tooltip = T("MagicBulletTooltip"),
        Callback = enableBulletHitManipulation
    })

    WarTycoonBox:AddToggle("Exploits_RocketManipulation", {
        Text = T("RocketHitManipulation"),
        Default = getgenv().Settings.Exploits.RocketManipulation,
        Tooltip = T("MagicRocketTooltip"),
        Callback = enableRocketHitManipulation
    })

    WarTycoonBox:AddToggle("Exploits_RPGSpam_Enabled", {
        Text = T("RPGSpam"),
        Default = getgenv().Settings.Exploits.RPGSpam.Enabled,
        Tooltip = T("RPGSpamTooltip"),
        Callback = function(value)
            getgenv().Settings.Exploits.RPGSpam.Enabled = value
        end,
    }):AddKeyPicker("Exploits_RPGSpam_Keybind", {
        Default = getgenv().Settings.Exploits.RPGSpam.Keybind,
        SyncToggleState = true,
        Mode = "Toggle",
        Text = T("RPGSpamKey"),
        Tooltip = T("RPGSpamKeyTooltip"),
        Callback = function()
            -- Logic handled by Heartbeat connection
        end,
    })

    WarTycoonBox:AddSlider("Exploits_RPGSpam_Count", {
        Text = T("RocketsPerSpam"),
        Default = getgenv().Settings.Exploits.RPGSpam.Count,
        Min = 1, Max = 500000, Rounding = 0,
        Tooltip = T("RocketsPerSpamTooltip"),
        Callback = function(value)
            getgenv().Settings.Exploits.RPGSpam.Count = math.floor(value)
        end,
    })

    WarTycoonBox:AddSlider("Exploits_RPGSpam_Speed", {
        Text = T("RocketsSpamSpeed"),
        Default = getgenv().Settings.Exploits.RPGSpam.Speed,
        Min = 0.1, Max = 5, Rounding = 1,
        Tooltip = T("RocketsSpamSpeedTooltip"),
        Callback = function(value)
            getgenv().Settings.Exploits.RPGSpam.Speed = value
        end,
    })

    WarTycoonBox:AddDropdown("Exploits_RPGSpam_Mode", {
        Text = T("SelectRocketMode"),
        Values = T("RocketModeValues"),
        Default = getgenv().Settings.Exploits.RPGSpam.Mode,
        Tooltip = T("RocketModeTooltip"),
        Multi = false,
        Callback = function(value)
            getgenv().Settings.Exploits.RPGSpam.Mode = value
        end,
    })

    WarTycoonBox:AddToggle("Exploits_QuickLagRPG_Enabled", {
        Text = T("QuickLagRPG"),
        Default = getgenv().Settings.Exploits.QuickLagRPG.Enabled,
        Tooltip = T("QuickLagRPGTooltip"),
        Callback = function(value)
            getgenv().Settings.Exploits.QuickLagRPG.Enabled = value
            if value then
                local camera, playerName = Camera, LocalPlayer.Name
                local repeatCount = 500

                local validWeapons = {"RPG", "Javelin", "Stinger"}

                local function getActiveWeapon()
                    for _, weaponName in ipairs(validWeapons) do
                        local weapon = workspace[playerName]:FindFirstChild(weaponName)
                        if weapon and weapon:IsA("Tool") and weapon.Parent == workspace[playerName] then
                            return weaponName
                        end
                    end
                    return nil
                end

                local function fireQuickLagRocket(weaponName)
                    if not weaponName then return end

                    local fireRocketVector = camera.CFrame.LookVector
                    local fireRocketPosition = camera.CFrame.Position
                    game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocket:InvokeServer(
                        fireRocketVector, workspace[playerName][weaponName], workspace[playerName][weaponName], fireRocketPosition
                    )

                    local fireRocketClientTable = {
                        ["expShake"] = {["fadeInTime"] = 0.05, ["magnitude"] = 3, ["rotInfluence"] = {0.4, 0, 0.4}, ["fadeOutTime"] = 0.5, ["posInfluence"] = {1, 1, 0}, ["roughness"] = 3},
                        ["gravity"] = Vector3.new(0, -20, 0), ["HelicopterDamage"] = 450, ["FireRate"] = 15, ["VehicleDamage"] = 350, ["ExpName"] = "Rocket",
                        ["RocketAmount"] = 1, ["ExpRadius"] = 12, ["BoatDamage"] = 300, ["TankDamage"] = 300, ["Acceleration"] = 8, ["ShieldDamage"] = 11170,
                        ["Distance"] = 4000, ["PlaneDamage"] = 500, ["GunshipDamage"] = 170, ["velocity"] = 200, ["ExplosionDamage"] = 120
                    }

                    local fireRocketClientInstance1 = game:GetService("ReplicatedStorage").RocketSystem.Rockets["RPG Rocket"]
                    local fireRocketClientInstance2 = workspace[playerName][weaponName]
                    local fireRocketClientInstance3 = workspace[playerName][weaponName]
                    game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocketClient:Fire(
                        camera.CFrame.Position, camera.CFrame.LookVector, fireRocketClientTable, fireRocketClientInstance1, fireRocketClientInstance2, fireRocketClientInstance3,
                        game:GetService("Players").LocalPlayer, nil, { [1] = workspace[playerName]:FindFirstChild(weaponName) }
                    )
                end

                local activeWeapon = getActiveWeapon()
                if activeWeapon then
                    for i = 1, repeatCount do
                        task.spawn(fireQuickLagRocket, activeWeapon)
                    end
                else
                    warn("No active weapon: RPG | JAVELIN | STINGER")
                end
            end
        end,
    }):AddKeyPicker("Exploits_QuickLagRPG_Keybind", {
        Default = getgenv().Settings.Exploits.QuickLagRPG.Keybind,
        Mode = "Toggle",
        Text = T("QuickLagRPGKey"),
        Tooltip = T("QuickLagRPGKeyTooltip"),
        Callback = function()
            -- Logic handled by the toggle callback
        end,
    })

    local HoldingStinger = false
    M.Button1Down:Connect(function()
        if getgenv().Settings.Exploits.StingerClick.Enabled then
            local function GetActiveStingerWeapon()
                if not getgenv().Settings.Exploits.MasterToggle then return end
                return (LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()):FindFirstChild("Stinger")
            end

            local function FireStingerRocket()
                local weapon = GetActiveStingerWeapon()
                if weapon then
                    local targetPosition = M.Hit.Position
                    local RS = game:GetService("ReplicatedStorage").RocketSystem.Events
                    RS.FireRocket:InvokeServer(Camera.CFrame.LookVector, weapon, weapon, Camera.CFrame.Position, nil, workspace.Terrain)
                    RS.RocketHit:FireServer(targetPosition, Camera.CFrame.LookVector, weapon, weapon, workspace.Terrain, workspace.Terrain, LocalPlayer.Name .. "Rocket")
                end
            end

            local function SpamStingerRockets()
                while HoldingStinger do
                    for i = 1, 100 do
                        if not HoldingStinger then break end
                        FireStingerRocket()
                    end
                end
            end

            if getgenv().Settings.Exploits.StingerClick.Mode == "Click" then
                FireStingerRocket()
            elseif getgenv().Settings.Exploits.StingerClick.Mode == "Click Hold Spam" then
                HoldingStinger = true
                for i = 1, 10 do
                    coroutine.wrap(SpamStingerRockets)()
                end
            end
        end
    end)
    M.Button1Up:Connect(function() HoldingStinger = false end)

    WarTycoonBox:AddToggle("Exploits_StingerClick_Enabled", {
        Text = T("StingerClick"),
        Default = getgenv().Settings.Exploits.StingerClick.Enabled,
        Tooltip = T("StingerClickTooltip"),
        Callback = function(state)
            getgenv().Settings.Exploits.StingerClick.Enabled = state
        end
    })

    WarTycoonBox:AddDropdown("Exploits_StingerClick_Mode", {
        AllowNull = false,
        Multi = false,
        Text = T("SelectClickMode"),
        Default = getgenv().Settings.Exploits.StingerClick.Mode,
        Values = T("ClickModeValues"),
        Tooltip = T("ClickModeTooltip"),
        Callback = function(value)
            getgenv().Settings.Exploits.StingerClick.Mode = value
        end
    })

    local antiLagConnection
    WarTycoonBox:AddToggle("Exploits_AntiLag", {
        Text = T("AntiLag"),
        Default = getgenv().Settings.Exploits.AntiLag,
        Tooltip = T("AntiLagTooltip"),
        Callback = function(value)
            getgenv().Settings.Exploits.AntiLag = value
            if not getgenv().Settings.Exploits.MasterToggle then
                if antiLagConnection then
                    antiLagConnection:Disconnect()
                    antiLagConnection = nil
                end
                return
            end

            if value then
                local visualRocketsFolder = workspace:WaitForChild("VisualRockets")
                for _, object in ipairs(visualRocketsFolder:GetChildren()) do
                    object:Destroy()
                end
                antiLagConnection = visualRocketsFolder.ChildAdded:Connect(function(newObject)
                    newObject:Destroy()
                end)
            else
                if antiLagConnection then
                    antiLagConnection:Disconnect()
                    antiLagConnection = nil
                end
            end
        end
    })

    local ACSEngineBox = ExploitTab:AddRightGroupbox(T("WeaponSettingsGroupBox"))
    ACSEngineBox:AddToggle("Exploits_WarTycoonMode", {
        Text = T("WarTycoonMode"),
        Default = getgenv().Settings.Exploits.WarTycoon,
        Tooltip = T("WarTycoonModeTooltip"),
        Callback = function(value)
            getgenv().Settings.Exploits.WarTycoon = value
        end
    })

    ACSEngineBox:AddToggle("Exploits_WeaponOnHands", {
        Text = T("WeaponInHands"),
        Default = getgenv().Settings.Exploits.WeaponOnHands,
        Tooltip = T("WeaponInHandsTooltip"),
        Callback = function(value)
            getgenv().Settings.Exploits.WeaponOnHands = value
        end
    })

    ACSEngineBox:AddButton(T("InfAmmo"), function()
        modifyWeaponSettings("Ammo", math.huge)
    end)

    ACSEngineBox:AddButton(T("NoRecoilNoSpread"), function()
        modifyWeaponSettings("VRecoil", {0, 0})
        modifyWeaponSettings("HRecoil", {0, 0})
        modifyWeaponSettings("MinSpread", 0)
        modifyWeaponSettings("MaxSpread", 0)
        modifyWeaponSettings("RecoilPunch", 0)
        modifyWeaponSettings("AimRecoilReduction", 0)
    end)

    ACSEngineBox:AddButton(T("InfBulletDistance"), function()
        modifyWeaponSettings("Distance", 25000)
    end)

    local bulletSpeedInput = ACSEngineBox:AddInput("WeaponMods_BulletSpeedInput", {
        Text = T("BulletSpeed"),
        Default = tostring(getgenv().Settings.Exploits.WeaponMods.BulletSpeed),
        Tooltip = T("BulletSpeedInputTooltip"),
        Callback = function(value)
            getgenv().Settings.Exploits.WeaponMods.BulletSpeed = tonumber(value) or 10000
        end
    })

    ACSEngineBox:AddButton(T("ChangeBulletSpeed"), function()
        modifyWeaponSettings("BSpeed", getgenv().Settings.Exploits.WeaponMods.BulletSpeed)
        modifyWeaponSettings("MuzzleVelocity", getgenv().Settings.Exploits.WeaponMods.BulletSpeed)
    end)

    local fireRateInput = ACSEngineBox:AddInput('WeaponMods_FireRateInput', {
        Text = T("FireRate"),
        Default = tostring(getgenv().Settings.Exploits.WeaponMods.FireRate),
        Tooltip = T("FireRateInputTooltip"),
    })

    ACSEngineBox:AddButton(T("ChangeFireRate"), function()
        getgenv().Settings.Exploits.WeaponMods.FireRate = tonumber(fireRateInput.Value) or 8888
        modifyWeaponSettings("FireRate", getgenv().Settings.Exploits.WeaponMods.FireRate)
        modifyWeaponSettings("ShootRate", getgenv().Settings.Exploits.WeaponMods.FireRate)
    end)

    local bulletsInput = ACSEngineBox:AddInput('WeaponMods_BulletsInput', {
        Text = T("Bullets"),
        Default = tostring(getgenv().Settings.Exploits.WeaponMods.Bullets),
        Tooltip = T("BulletsInputTooltip"),
        Numeric = true
    })

    ACSEngineBox:AddButton(T("MultiBullets"), function()
        getgenv().Settings.Exploits.WeaponMods.Bullets = tonumber(bulletsInput.Value) or 50
        modifyWeaponSettings("Bullets", getgenv().Settings.Exploits.WeaponMods.Bullets)
    end)

    local fireModeInput = ACSEngineBox:AddInput('WeaponMods_FireModeInput', {
        Text = T("FireMode"),
        Default = getgenv().Settings.Exploits.WeaponMods.FireMode,
        Tooltip = T("FireModeInputTooltip"),
    })

    ACSEngineBox:AddButton(T("ChangeFireMode"), function()
        getgenv().Settings.Exploits.WeaponMods.FireMode = fireModeInput.Value or 'Auto'
        modifyWeaponSettings("Mode", getgenv().Settings.Exploits.WeaponMods.FireMode)
    end)

    -- --- Visuals Tab ---
    local espbox = VisualsTab:AddLeftGroupbox(T("ESPGroupBox"))

    espbox:AddToggle("ESP_TeamCheck", {
        Text = T("EnableTeamCheck"),
        Default = getgenv().Settings.ESP.TeamCheck,
        Tooltip = T("TeamCheckTooltip"),
        Callback = function(state)
            getgenv().Settings.ESP.TeamCheck = state
        end,
    })

    espbox:AddToggle("ESP_TeamColor", {
        Text = T("ESPTeamColor"),
        Default = getgenv().Settings.ESP.TeamColor,
        Tooltip = "Uses team colors for ESP visuals.",
        Callback = function(state)
            getgenv().Settings.ESP.TeamColor = state
            for _, player in pairs(Players:GetPlayers()) do
                updateTeamColor(player)
            end
        end,
    })

    espbox:AddToggle("ESP_Box_Enabled", {
        Text = T("BoxESP"),
        Default = getgenv().Settings.ESP.Box.Enabled,
        Tooltip = T("BoxESPToggleTooltip"),
        Callback = function(state)
            getgenv().Settings.ESP.Box.Enabled = state
        end,
    }):AddColorPicker("ESP_Box_Color", {
        Text = T("BoxColor"),
        Default = getgenv().Settings.ESP.Box.Color,
        Callback = function(color)
            getgenv().Settings.ESP.Box.Color = color
            for _, box in pairs(espBoxes) do
                box.Color = color
            end
        end,
    })

    espbox:AddToggle("ESP_NameTags_Enabled", {
        Text = T("EnableNameTags"),
        Default = getgenv().Settings.ESP.NameTags.Enabled,
        Tooltip = T("NameTagsToggleTooltip"),
        Callback = function(state)
            getgenv().Settings.ESP.NameTags.Enabled = state
        end,
    }):AddColorPicker("ESP_NameTags_Color", {
        Text = T("NameTagColor"),
        Default = getgenv().Settings.ESP.NameTags.Color,
        Callback = function(color)
            getgenv().Settings.ESP.NameTags.Color = color
            for _, nameTag in pairs(espNameTags) do
                nameTag.Color = color
            end
        end,
    })

    espbox:AddToggle("ESP_Tracers_Enabled", {
        Text = T("EnableTracers"),
        Default = getgenv().Settings.ESP.Tracers.Enabled,
        Tooltip = T("TracersToggleTooltip"),
        Callback = function(state)
            getgenv().Settings.ESP.Tracers.Enabled = state
        end,
    }):AddColorPicker("ESP_Tracers_Color", {
        Text = T("TracerColor"),
        Default = getgenv().Settings.ESP.Tracers.Color,
        Callback = function(color)
            getgenv().Settings.ESP.Tracers.Color = color
            for _, tracer in pairs(espTracers) do
                tracer.Color = color
            end
        end,
    })

    local worldbox = VisualsTab:AddRightGroupbox(T("WorldGroupBox"))

    worldbox:AddSlider("World_ClockTime", {
        Text = T("ClockTime"), Default = getgenv().Settings.World.ClockTime, Min = 0, Max = 24, Rounding = 1,
        Tooltip = T("ClockTimeTooltip"),
        Callback = function(v)
            getgenv().Settings.World.ClockTime = v
            Lighting.ClockTime = v
        end,
    })

    worldbox:AddSlider("World_FOV_Value", {
        Text = T("FOV"), Default = getgenv().Settings.World.FOV.Value, Min = 30, Max = 120, Rounding = 2,
        Tooltip = T("FOVTooltip"),
        Callback = function(v)
            getgenv().Settings.World.FOV.Value = v
        end,
    })

    worldbox:AddToggle("World_FOV_Enabled", {
        Text = T("EnableFOVChange"), Default = getgenv().Settings.World.FOV.Enabled,
        Tooltip = T("EnableFOVChangeTooltip"),
        Callback = function(state)
            getgenv().Settings.World.FOV.Enabled = state
        end,
    })

    worldbox:AddToggle("World_Nebula_Enabled", {
        Text = T("NebulaTheme"), Default = getgenv().Settings.World.Nebula.Enabled,
        Tooltip = T("NebulaThemeTooltip"),
        Callback = function(state)
            getgenv().Settings.World.Nebula.Enabled = state
            if state then
                local b = Instance.new("BloomEffect", Lighting) b.Intensity, b.Size, b.Threshold, b.Name = 0.7, 24, 1, "NebulaBloom"
                local c = Instance.new("ColorCorrectionEffect", Lighting) c.Saturation, c.Contrast, c.TintColor, c.Name = 0.5, 0.2, getgenv().Settings.World.Nebula.Color, "NebulaColorCorrection"
                local a = Instance.new("Atmosphere", Lighting) a.Density, a.Offset, a.Glare, a.Haze, a.Color, a.Decay, a.Name = 0.4, 0.25, 1, 2, getgenv().Settings.World.Nebula.Color, Color3.fromRGB(25, 25, 112), "NebulaAtmosphere"
                Lighting.Ambient, Lighting.OutdoorAmbient = getgenv().Settings.World.Nebula.Color, getgenv().Settings.World.Nebula.Color
                Lighting.FogStart, Lighting.FogEnd = 100, 500
                Lighting.FogColor = getgenv().Settings.World.Nebula.Color
            else
                for _, v in pairs({"NebulaBloom", "NebulaColorCorrection", "NebulaAtmosphere"}) do
                    local obj = Lighting:FindFirstChild(v) if obj then obj:Destroy() end
                end
                Lighting.Ambient, Lighting.OutdoorAmbient = originalAmbient, originalOutdoorAmbient
                Lighting.FogStart, Lighting.FogEnd = originalFogStart, originalFogEnd
                Lighting.FogColor = originalFogColor
            end
        end,
    }):AddColorPicker("World_Nebula_Color", {
        Text = T("NebulaColor"), Default = getgenv().Settings.World.Nebula.Color,
        Tooltip = T("NebulaColorTooltip"),
        Callback = function(c)
            getgenv().Settings.World.Nebula.Color = c
            if getgenv().Settings.World.Nebula.Enabled then
                local nc = Lighting:FindFirstChild("NebulaColorCorrection") if nc then nc.TintColor = c end
                local na = Lighting:FindFirstChild("NebulaAtmosphere") if na then na.Color = c end
                Lighting.Ambient, Lighting.OutdoorAmbient = c, c
                Lighting.FogColor = c
            end
        end,
    })

    local SkyboxDropdown = worldbox:AddDropdown("World_SkyboxSelector", {
        AllowNull = false,
        Text = T("SelectSkybox"),
        Default = getgenv().Settings.World.Skybox,
        Values = SkyboxNames,
        Tooltip = T("SelectSkyboxTooltip"),
    }):OnChanged(function(SelectedSkybox)
        getgenv().Settings.World.Skybox = SelectedSkybox
        if Skyboxes[SelectedSkybox] then
            Visuals:SwitchSkybox(SelectedSkybox)
        end
    end)
end

-- Initial UI setup
updateUI()
ThemeManager:LoadDefaultTheme()
