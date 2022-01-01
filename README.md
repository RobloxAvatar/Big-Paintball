_G.Aimbot = false
_G.Esp = false

local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()

local Window = Library.CreateLib("Big Paintball", "Serpent")

local MainTab = Window:NewTab("Main")

local MainSection = MainTab:NewSection("Main")

oldDamage = nil
oldFireRate = nil
oldVelocity = nil
oldAutomatic = nil
oldAdditionalSpeed = nil
oldGadgetDamage = nil

for i,v in next, getgc(true) do
    if type(v) == "table" and rawget(v, "damage") then
        oldDamage = v.damage
        oldFireRate = v.firerate
        oldVelocity = v.velocity
        oldAutomatic = v.automatic
        oldAdditionalSpeed = v.additionalspeed
        oldGadgetDamage = v.gadgetDamage
    end
end

MainSection:NewToggle("Gun Mod", "toggle the gun mod!", function(state)
    if state then
        toggled = true
        if toggled == true then
            for i,v in next, getgc(true) do
                if type(v) == "table" and rawget(v, "damage") then
                    v.damage = math.huge
                    v.firerate = 0.000000001
                    v.velocity = 50000
                    v.automatic = true
                    v.additionalspeed = 10
                    v.gadgetDamage = math.huge
                end
            end
        end
    else
        toggled = false
        if toggled == false then
            for i,v in next, getgc(true) do
                if type(v) == "table" and rawget(v, "damage") then
                    v.damage = oldDamage
                    v.firerate = oldFireRate
                    v.velocity = oldVelocity
                    v.automatic = oldAutomatic
                    v.additionalspeed = oldAdditionalSpeed 
                    v.gadgetDamage = oldGadgetDamage
                end
            end
        end
    end
end)

MainSection:NewToggle("Aimbot", "toggles the aimbot!", function(state)
    if state then
        _G.Aimbot = true
    else
        _G.Aimbot = false
    end
end)

local esp_settings = {
    textsize = 8,
    colour = 255,255,255
}

local gui = Instance.new("BillboardGui")
local esp = Instance.new("TextLabel",gui)

gui.Name = "Cracked esp";
gui.ResetOnSpawn = false
gui.AlwaysOnTop = true;
gui.LightInfluence = 0;
gui.Size = UDim2.new(1.75, 0, 1.75, 0);
esp.BackgroundColor3 = Color3.fromRGB(255, 255, 255);
esp.Text = ""
esp.Size = UDim2.new(0.0001, 0.00001, 0.0001, 0.00001);
esp.BorderSizePixel = 4;
esp.BorderColor3 = Color3.new(esp_settings.colour)
esp.BorderSizePixel = 0
esp.Font = "GothamSemibold"
esp.TextSize = esp_settings.textsize
esp.TextColor3 = Color3.fromRGB(esp_settings.colour)

MainSection:NewToggle("Esp", "toggles the esp!", function(state)
    if state then
        _G.Esp = true
        game:GetService("RunService").RenderStepped:Connect(function()
            for i,v in pairs (game:GetService("Players"):GetPlayers()) do
                if v ~= game:GetService("Players").LocalPlayer and v.Character.Head:FindFirstChild("Cracked esp")==nil and v.TeamColor ~= game:GetService("Players").LocalPlayer.TeamColor and _G.Esp == true then
                    esp.Text = "{"..v.Name.."}"
                    gui:Clone().Parent = v.Character.Head
                end
            end
        end)
    else
        _G.Esp = false
        game:GetService("RunService").RenderStepped:Connect(function()
            if _G.Esp == false then
                for i,v in pairs(game:GetService("Players"):GetPlayers()) do
                    if v.Character.Head:FindFirstChildOfClass("BillboardGui") and v.TeamColor ~= game:GetService("Players").LocalPlayer.TeamColor then
                        v.Character.Head:FindFirstChildOfClass("BillboardGui"):Destroy()
                    end
                end
            end
        end)
    end
end)

local camera = game:GetService("Workspace").CurrentCamera
local plr = game:GetService("Players").LocalPlayer
local dist = math.huge
local target = nil

function closestplayer()
    for i,v in pairs(game:GetService("Players"):GetPlayers()) do
        if v ~= plr and v.Character and v.Character:FindFirstChild("Head") and v.TeamColor ~= plr.TeamColor and _G.Aimbot then
            local magnitude = (v.Character.Head.Position - plr.Character.Head.Position).magnitude
            if magnitude < dist then
                dist = magnitude
                target = v
            end
        end
    end
    return target
end

local aiming = false

game:GetService("UserInputService").InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton2 then
        aiming = true 
    end
end)

game:GetService("UserInputService").InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton2 then
        aiming = false 
    end
end)

game:GetService("RunService").RenderStepped:Connect(function()
    if _G.Aimbot == true and aiming == true then
        camera.CFrame = CFrame.new(camera.CFrame.Position, closestplayer().Character.Head.Position) 
    end
end)

MainSection:NewButton("Unlock All Weapons", "unlocks all weapons!", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/RobloxAvatar/Big-Paintball/main/Unlock-All.lua", true))()
end)

MainSection:NewButton("Silent Aim", "opens owl hub for silent aim", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/CriShoux/OwlHub/master/OwlHub.txt", true))()
end)
