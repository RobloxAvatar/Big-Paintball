--[[
    [Updated]: 1/2/22
    [Fixed]: Aimbot Teamcheck
]]

_G.AimbotEnabled = false
_G.CircleVisible = false
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
        _G.AimbotEnabled = true
        _G.CircleVisible = true
    else
        _G.AimbotEnabled = false
        _G.CircleVisible = false
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

local Camera = workspace.CurrentCamera
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local Holding = false

_G.TeamCheck = true
_G.AimPart = "Head"
_G.Sensitivity = 0

_G.CircleSides = 64
_G.CircleColor = Color3.fromRGB(255, 255, 255)
_G.CircleTransparency = 0.7
_G.CircleRadius = 150
_G.CircleFilled = false
_G.CircleThickness = 0

local FOVCircle = Drawing.new("Circle")
FOVCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
FOVCircle.Radius = _G.CircleRadius
FOVCircle.Filled = _G.CircleFilled
FOVCircle.Color = _G.CircleColor
FOVCircle.Visible = _G.CircleVisible
FOVCircle.Radius = _G.CircleRadius
FOVCircle.Transparency = _G.CircleTransparency
FOVCircle.NumSides = _G.CircleSides
FOVCircle.Thickness = _G.CircleThickness

local function GetClosestPlayer()
	local MaximumDistance = _G.CircleRadius
	local Target = nil

	for _, v in next, Players:GetPlayers() do
		if v.Name ~= LocalPlayer.Name then
			if _G.TeamCheck == true then
				if v.Team ~= LocalPlayer.Team then
					if v.Character ~= nil then
						if v.Character:FindFirstChild("HumanoidRootPart") ~= nil then
							if v.Character:FindFirstChild("Humanoid") ~= nil and v.Character:FindFirstChild("Humanoid").Health ~= 0 then
								local ScreenPoint = Camera:WorldToScreenPoint(v.Character:WaitForChild("HumanoidRootPart", math.huge).Position)
								local VectorDistance = (Vector2.new(UserInputService:GetMouseLocation().X, UserInputService:GetMouseLocation().Y) - Vector2.new(ScreenPoint.X, ScreenPoint.Y)).Magnitude
								
								if VectorDistance < MaximumDistance then
									Target = v
								end
							end
						end
					end
				end
			else
				if v.Character ~= nil then
					if v.Character:FindFirstChild("HumanoidRootPart") ~= nil then
						if v.Character:FindFirstChild("Humanoid") ~= nil and v.Character:FindFirstChild("Humanoid").Health ~= 0 then
							local ScreenPoint = Camera:WorldToScreenPoint(v.Character:WaitForChild("HumanoidRootPart", math.huge).Position)
							local VectorDistance = (Vector2.new(UserInputService:GetMouseLocation().X, UserInputService:GetMouseLocation().Y) - Vector2.new(ScreenPoint.X, ScreenPoint.Y)).Magnitude
							
							if VectorDistance < MaximumDistance then
								Target = v
							end
						end
					end
				end
			end
		end
	end

	return Target
end

UserInputService.InputBegan:Connect(function(Input)
    if Input.UserInputType == Enum.UserInputType.MouseButton2 then
        Holding = true
    end
end)

UserInputService.InputEnded:Connect(function(Input)
    if Input.UserInputType == Enum.UserInputType.MouseButton2 then
        Holding = false
    end
end)

RunService.RenderStepped:Connect(function()
    FOVCircle.Position = Vector2.new(UserInputService:GetMouseLocation().X, UserInputService:GetMouseLocation().Y)
    FOVCircle.Radius = _G.CircleRadius
    FOVCircle.Filled = _G.CircleFilled
    FOVCircle.Color = _G.CircleColor
    FOVCircle.Visible = _G.CircleVisible
    FOVCircle.Radius = _G.CircleRadius
    FOVCircle.Transparency = _G.CircleTransparency
    FOVCircle.NumSides = _G.CircleSides
    FOVCircle.Thickness = _G.CircleThickness

    if Holding == true and _G.AimbotEnabled == true then
        TweenService:Create(Camera, TweenInfo.new(_G.Sensitivity, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {CFrame = CFrame.new(Camera.CFrame.Position, GetClosestPlayer().Character[_G.AimPart].Position)}):Play()
    end
end)

MainSection:NewButton("Unlock All Weapons", "unlocks all weapons!", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/RobloxAvatar/Big-Paintball/main/Unlock-All.lua", true))()
end)
