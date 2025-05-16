-- Blox Fruits Ultimate Script with Red Interactive Interface
-- Compatible with modern Roblox executors (Synapse X, KRNL, Fluxus, etc.)
-- Use at your own risk; may violate Roblox/Blox Fruits terms of service

-- Wait for game to load
repeat wait() until game:IsLoaded() and game.Players.LocalPlayer

-- Services
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local VirtualUser = game:GetService("VirtualUser")

-- Player and Character
local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")
local Backpack = Player.Backpack

-- UI Setup using Drawing Library (BracketV3 for Interface)
local DrawingLibrary = loadstring(game:HttpGet("https://raw.githubusercontent.com/1201for/v3rmillion.net/main/BracketV3.lua"))()
local UI = DrawingLibrary:CreateWindow("Blox Fruits Ultimate Script")

-- Styling the UI (Red Theme with Interactive Buttons)
UI.BackgroundColor = Color3.fromRGB(50, 0, 0) -- Dark Red Background
UI.TitleColor = Color3.fromRGB(255, 255, 255) -- White Title
UI.ButtonColor = Color3.fromRGB(200, 0, 0) -- Red Buttons
UI.TextColor = Color3.fromRGB(255, 255, 255) -- White Text
UI.ButtonHoverColor = Color3.fromRGB(255, 50, 50) -- Lighter Red on Hover

-- Initial Grants (Gamepasses, Permanent Fruits, Dark Blade)
local function GrantGamepassesAndFruits()
    -- Grant All Gamepasses
    ReplicatedStorage.Remotes.CommF_:InvokeServer("SetEXPBoost", 2)
    ReplicatedStorage.Remotes.CommF_:InvokeServer("SetBeliBoost", 2)
    ReplicatedStorage.Remotes.CommF_:InvokeServer("UnlockRaceChange")
    ReplicatedStorage.Remotes.CommF_:InvokeServer("AddItem", "Triple Dark Blade V2")
    if Humanoid then
        Humanoid.WalkSpeed = 32
    end

    -- Grant Permanent Fruits
    local permanentFruits = {"Dragon", "Leopard", "Kitsune", "Dough", "Gravity", "Dark", "T-Rex"}
    for _, fruit in pairs(permanentFruits) do
        ReplicatedStorage.Remotes.CommF_:InvokeServer("AddFruit", fruit .. " Permanent")
        wait(0.1 + math.random() * 0.2)
    end

    -- Grant Dark Blade
    ReplicatedStorage.Remotes.CommF_:InvokeServer("AddItem", "Dark Blade")
end
GrantGamepassesAndFruits()

-- Variables
local Toggles = {
    AutoFarm = false,
    AutoCollectFruit = false,
    DojoMissions = false,
    MaritimeEvent = false,
    IslandDetection = false,
    GearForV4 = false,
    SoloTrial = false,
    FruitRainUpdated = false,
    DracoRaceMissions = false,
    LeviathanEncounter = false,
    AntiCheat = false,
    CustomDamage = false,
    BerryLocations = false,
    KatakuriTimer = false
}

-- Custom Damage Value
local CustomDamageMultiplier = 1

-- 2x XP Permanent
local function SetDoubleXP()
    ReplicatedStorage.Remotes.CommF_:InvokeServer("SetEXPBoost", 2)
    wait(5 + math.random() * 2)
end
spawn(function()
    while true do
        SetDoubleXP()
        wait(10)
    end
end)

-- Functions
local function TeleportTo(targetCFrame)
    if HumanoidRootPart then
        HumanoidRootPart.CFrame = targetCFrame
        wait(0.5 + math.random() * 0.3) -- Random delay for anti-detect
    end
end

local function AttackTarget(target)
    if target and target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
        TeleportTo(target.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0))
        VirtualUser:CaptureController()
        VirtualUser:ClickButton1(Vector2.new())
        wait(0.1 + math.random() * 0.5) -- Random delay for human-like behavior
    end
end

-- Customizable Auto Farm
local function AutoFarm()
    while Toggles.AutoFarm do
        local farmMethod = UI:Prompt("Choose Farming Method", {"Sword", "Fruit", "Melee"}, 1)
        local level = Player.Data.Level.Value
        local farmLocation
        if level < 300 then -- First Sea
            farmLocation = CFrame.new(1038, 25, 1365) -- Near Cafe
        elseif level < 1500 then -- Second Sea
            farmLocation = CFrame.new(-1325, 150, -750) -- Near Mansion
        else -- Third Sea
            farmLocation = CFrame.new(-12400, 300, -7000) -- Floating Turtle
        end
        TeleportTo(farmLocation)
        local enemies = Workspace.Enemies:GetChildren()
        for _, enemy in pairs(enemies) do
            if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                -- Adjust Damage Based on User Preference
                enemy.Humanoid:TakeDamage(enemy.Humanoid.MaxHealth * CustomDamageMultiplier)
                AttackTarget(enemy)
            end
        end
        wait(0.1 + math.random() * 0.3)
    end
end

-- Auto Collect Fruit
local function AutoCollectFruit()
    while Toggles.AutoCollectFruit do
        for _, fruit in pairs(Workspace:GetChildren()) do
            if fruit:IsA("Tool") and string.find(fruit.Name, "Fruit") then
                TeleportTo(fruit.Handle.CFrame)
                fruit.Parent = Character
                wait(0.5 + math.random() * 0.3)
            end
        end
        wait(0.1 + math.random() * 0.2)
    end
end

-- Dojo Missions
local function DojoMissions()
    while Toggles.DojoMissions do
        local dojo = Workspace:FindFirstChild("DragonDojo")
        if dojo then
            TeleportTo(dojo.PrimaryPart.CFrame)
            local enemies = dojo.Enemies:GetChildren()
            for _, enemy in pairs(enemies) do
                AttackTarget(enemy)
            end
        end
        wait(1 + math.random() * 0.5)
    end
end

-- Maritime Event Missions (Prehistoric Island)
local function MaritimeEvent()
    while Toggles.MaritimeEvent do
        local seaEnemies = Workspace.SeaEnemies:GetChildren()
        for _, enemy in pairs(seaEnemies) do
            if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                TeleportTo(enemy.HumanoidRootPart.CFrame)
                AttackTarget(enemy)
            end
        end
        -- Check for Prehistoric Island
        local prehistoricIsland = Workspace:FindFirstChild("PrehistoricIsland")
        if prehistoricIsland then
            TeleportTo(prehistoricIsland.PrimaryPart.CFrame)
            local relic = prehistoricIsland:FindFirstChild("FossilAncientRelic")
            if relic then
                fireclickdetector(relic:FindFirstChildOfClass("ClickDetector"))
                wait(1)
                local lavaGolems = prehistoricIsland.Enemies:GetChildren()
                for _, golem in pairs(lavaGolems) do
                    AttackTarget(golem)
                end
            end
        end
        wait(0.1 + math.random() * 0.3)
    end
end

-- Island Detection in Sea
local function IslandDetection()
    while Toggles.IslandDetection do
        local islands = Workspace:GetChildren()
        for _, island in pairs(islands) do
            if island.Name == "PrehistoricIsland" or island.Name == "MirageIsland" then
                UI:Notify("Island Detected: " .. island.Name .. " at " .. tostring(island.PrimaryPart.Position))
                TeleportTo(island.PrimaryPart.CFrame)
            end
        end
        wait(5 + math.random() * 2)
    end
end

-- Gear for V4 and Solo Trial System
local function GearForV4()
    while Toggles.GearForV4 do
        local gearLocation = CFrame.new(-9681, 125, 6285) -- Near Hydra Island (Third Sea)
        TeleportTo(gearLocation)
        local gear = Workspace:FindFirstChild("GearForV4")
        if gear then
            fireclickdetector(gear:FindFirstChildOfClass("ClickDetector"))
        end
        wait(1 + math.random() * 0.5)
    end
end

local function SoloTrial()
    while Toggles.SoloTrial do
        ReplicatedStorage.Remotes.CommF_:InvokeServer("StartTrial", "Solo")
        local trialArea = Workspace:FindFirstChild("TrialArea")
        if trialArea then
            TeleportTo(trialArea.PrimaryPart.CFrame)
            local trialEnemies = trialArea.Enemies:GetChildren()
            for _, enemy in pairs(trialEnemies) do
                AttackTarget(enemy)
            end
        end
        wait(1 + math.random() * 0.5)
    end
end

-- Updated Fruit Rain
local function FruitRainUpdated()
    while Toggles.FruitRainUpdated do
        local modernFruits = {"Dragon Rework", "Gravity", "Leopard", "Kitsune", "Dough", "Dark"}
        for _, fruit in pairs(modernFruits) do
            ReplicatedStorage.Remotes.CommF_:InvokeServer("SpawnFruit", fruit)
        end
        wait(5 + math.random() * 2)
    end
end

-- Draco Race Missions
local function DracoRaceMissions()
    while Toggles.DracoRaceMissions do
        local dragonHunter = Workspace.NPCs:FindFirstChild("Dragon Hunter")
        if dragonHunter then
            TeleportTo(dragonHunter.CFrame)
            fireclickdetector(dragonHunter:FindFirstChildOfClass("ClickDetector"))
            wait(1)
            local enemies = Workspace.Enemies:GetChildren()
            for _, enemy in pairs(enemies) do
                if enemy.Name == "Hydra Enforcer" or enemy.Name == "Venomous Assailant" then
                    AttackTarget(enemy)
                end
            end
        end
        wait(1 + math.random() * 0.5)
    end
end

-- Leviathan Encounter and Solo Kill
local function LeviathanEncounter()
    while Toggles.LeviathanEncounter do
        local leviathan = Workspace:FindFirstChild("Leviathan")
        if leviathan and leviathan:FindFirstChild("Humanoid") then
            TeleportTo(leviathan.HumanoidRootPart.CFrame)
            AttackTarget(leviathan)
        else
            UI:Notify("Searching for Leviathan...")
            local seaLocation = CFrame.new(0, 100, 0) -- Center of the sea
            TeleportTo(seaLocation)
        end
        wait(0.1 + math.random() * 0.3)
    end
end

-- Anti-Cheat System (Simulate Human Behavior)
local function AntiCheat()
    while Toggles.AntiCheat do
        HumanoidRootPart.CFrame = HumanoidRootPart.CFrame + Vector3.new(math.random(-10, 10), 0, math.random(-10, 10))
        wait(math.random(1, 3))
        Humanoid.Jump = true
        wait(0.2)
        Humanoid.Jump = false
        local npcs = Workspace.NPCs:GetChildren()
        if #npcs > 0 then
            local randomNPC = npcs[math.random(1, #npcs)]
            TeleportTo(randomNPC.CFrame)
            wait(0.5)
        end
        wait(5 + math.random() * 5)
    end
end

-- Custom Damage Adjustment
local function CustomDamage()
    while Toggles.CustomDamage do
        local damageInput = UI:Prompt("Set Damage Multiplier (1-10)", {"1", "2", "3", "4", "5", "6", "7", "8", "9", "10"}, 1)
        CustomDamageMultiplier = tonumber(damageInput)
        UI:Notify("Damage Multiplier set to " .. CustomDamageMultiplier .. "x")
        wait(1)
    end
end

-- Berry Spawn Locations
local function BerryLocations()
    while Toggles.BerryLocations do
        local berries = Workspace:GetChildren()
        for _, berry in pairs(berries) do
            if berry.Name == "Berry" then
                UI:Notify("Berry found at " .. tostring(berry.Position))
            end
        end
        wait(5 + math.random() * 2)
    end
end

-- Katakuri Spawn Timer (Estimate)
local function KatakuriTimer()
    while Toggles.KatakuriTimer do
        local katakuri = Workspace.Enemies:FindFirstChild("Katakuri")
        if katakuri then
            UI:Notify("Katakuri is currently spawned!")
        else
            -- Estimate based on average spawn time (every 4 hours)
            local lastSpawn = Player.Data.LastKatakuriSpawn or 0
            local currentTime = os.time()
            local timeSinceLastSpawn = currentTime - lastSpawn
            local spawnInterval = 4 * 3600 -- 4 hours in seconds
            local timeLeft = spawnInterval - timeSinceLastSpawn
            if timeLeft <= 0 then
                UI:Notify("Katakuri may spawn soon!")
            else
                local minutesLeft = math.floor(timeLeft / 60)
                UI:Notify("Katakuri may spawn in " .. minutesLeft .. " minutes")
            end
        end
        wait(60) -- Check every minute
    end
end

-- UI Buttons and Triggers
UI:Button("Auto Farm", function() Toggles.AutoFarm = not Toggles.AutoFarm; if Toggles.AutoFarm then AutoFarm() end end)
UI:Button("Auto Collect Fruit", function() Toggles.AutoCollectFruit = not Toggles.AutoCollectFruit; if Toggles.AutoCollectFruit then AutoCollectFruit() end end)
UI:Button("Dojo Missions", function() Toggles.DojoMissions = not Toggles.DojoMissions; if Toggles.DojoMissions then DojoMissions() end end)
UI:Button("Maritime Event", function() Toggles.MaritimeEvent = not Toggles.MaritimeEvent; if Toggles.MaritimeEvent then MaritimeEvent() end end)
UI:Button("Island Detection", function() Toggles.IslandDetection = not Toggles.IslandDetection; if Toggles.IslandDetection then IslandDetection() end end)
UI:Button("Gear for V4", function() Toggles.GearForV4 = not Toggles.GearForV4; if Toggles.GearForV4 then GearForV4() end end)
UI:Button("Solo Trial", function() Toggles.SoloTrial = not Toggles.SoloTrial; if Toggles.SoloTrial then SoloTrial() end end)
UI:Button("Fruit Rain (Updated)", function() Toggles.FruitRainUpdated = not Toggles.FruitRainUpdated; if Toggles.FruitRainUpdated then FruitRainUpdated() end end)
UI:Button("Draco Race Missions", function() Toggles.DracoRaceMissions = not Toggles.DracoRaceMissions; if Toggles.DracoRaceMissions then DracoRaceMissions() end end)
UI:Button("Leviathan Encounter", function() Toggles.LeviathanEncounter = not Toggles.LeviathanEncounter; if Toggles.LeviathanEncounter then LeviathanEncounter() end end)
UI:Button("Anti-Cheat", function() Toggles.AntiCheat = not Toggles.AntiCheat; if Toggles.AntiCheat then AntiCheat() end end)
UI:Button("Custom Damage", function() Toggles.CustomDamage = not Toggles.CustomDamage; if Toggles.CustomDamage then CustomDamage() end end)
UI:Button("Berry Locations", function() Toggles.BerryLocations = not Toggles.BerryLocations; if Toggles.BerryLocations then BerryLocations() end end)
UI:Button("Katakuri Timer", function() Toggles.KatakuriTimer = not Toggles.KatakuriTimer; if Toggles.KatakuriTimer then KatakuriTimer() end end)

-- Main Loop to Keep UI Active
while true do
    wait(0.1)
end
