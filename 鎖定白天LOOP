
-- ========================================================
-- [TXR 核心工具] 測試專用版 (強制輸出，不攔截)
-- ========================================================

-- [工具] UDS 數值轉 HH:MM (專門處理 1930.0 這種格式)
local function FormatToTime(uds_value)
    if not uds_value then return "N/A" end
    local total = math.floor(uds_value)
    local h = math.floor(total / 100)
    local m = math.floor(total % 100)
    return string.format("%02d:%02d", h, m)
end

-- [工具] 強制顯示版 TXRLog
local function TXRLog(title, lines)
    -- 先暫時移除地圖判斷與過濾鎖，確保我們能看到東西
    local indent = "      "
    local output = "\n" .. indent .. ">>> [" .. title .. "] <<<\n"
    if lines then
        for _, line in ipairs(lines) do
            output = output .. indent .. "> " .. line .. "\n"
        end
    end
    print(output .. indent .. "------------------------")
end


-- =================== CONFIG (配置區) ===================
UseSystemTime            = false  -- [設定] 是否使用電腦系統時間
TimeZoneOffset           = 0      -- [設定] 時區偏移量
DuskNightDawnOnly        = false  -- [設定] 是否僅在 黃昏/夜晚/黎明 循環
RandomizeClouds          = false  -- [設定] 進入地圖時是否隨機雲量
RandomizeFog             = false  -- [設定] 進入地圖時是否隨機霧量

-- [時間循環設置]
DayLoopEnabled           = true   -- [設定] 是否啟用 05:00-19:00 循環 (鎖定白天)
DayLoopStart             = 650    -- [設定] 循環開始時間 (06:30)
DayLoopEnd               = 1950   -- [設定] 循環結束時間 (19:30)

-- [HUD 設置]
HUD_Enabled              = true   -- [設定] 是否顯示畫面資訊 (預設開啟)
HUD_X                    = 0.75   -- [設定] HUD X軸位置 (0~1)
HUD_Y                    = 0.05   -- [設定] HUD Y軸位置 (0~1)

-- 日誌與基礎設置
AutoEnableLogging        = true   -- [設定] 自動開啟日誌
VerboseConsole           = true   -- [設定] 控制台詳細輸出
SimulationSpeed          = 50    -- [設定] 時間流逝速度倍率
BaselineTimeSpeed        = 3      -- [設定] 基礎時間速度
EPS_TOD                  = 0.5    -- [設定] 時間誤差容許值

-- 車燈邏輯閾值 (決定何時開關燈)
HeadlightOffThresholdTOD = 700    -- [設定] 關燈時間 (07:00)
HeadlightOnThresholdTOD  = 1850   -- [設定] 開燈時間 (18:50)
HeadlightDawnOffOffsetUDS = 50    -- [設定] 黎明偏移量
HeadlightDuskOnOffsetUDS  = 50    -- [設定] 黃昏偏移量
HeadlightManualComboEnabled = true -- [設定] 是否啟用手動開關燈組合鍵 (L1+Up)

-- 過渡與減速設置 (日夜交替時的平滑處理)
TransitionAutoEnabled    = true   -- [設定] 自動過渡減速
TransitionSlowSpeed      = 15     -- [設定] 過渡時的速度
TransitionWindowTOD      = 200    -- [設定] 過渡窗口大小
TransitionHoldSeconds    = 0.0    -- [設定] 保持時間
TransitionMinGapSeconds  = 0.5    -- [設定] 最小間隔時間

-- 慢速窗口 (晨昏減速區間)
SlowWindowDawnStart = 530
SlowWindowDawnEnd   = 700  
SlowWindowDuskStart = 1750
SlowWindowDuskEnd   = 1950
SlowSafetyRestoreMarginUDS = 12

-- ========================================================
-- [2. 配置區] 出發模式與全域種子
-- ========================================================
local SpawnModes = { "STATIC", "PERSISTENT", "RANDOM" }
local CurrentSpawnModeIndex = 1
_G.TXR_RANDOM_DAY_SEED = nil
_G.TXR_RANDOM_DAY_LOCKED = false

local function GetCurrentSpawnMode() return SpawnModes[CurrentSpawnModeIndex] end

-- [函數] 重新計算車燈閾值
local function _RecomputeHeadlightThresholds()
  local dawnEnd = SlowWindowDawnEnd or 730
  local duskEnd = SlowWindowDuskEnd or 1950
  HeadlightOffThresholdTOD = math.max(0, dawnEnd - (HeadlightDawnOffOffsetUDS or 50))
  HeadlightOnThresholdTOD  = math.max(0, duskEnd - (HeadlightDuskOnOffsetUDS or 50))
end
_RecomputeHeadlightThresholds()

-- 東京色調設置 (畫面濾鏡)
TokyoTintEnabled         = true
TokyoTintMaxStrength     = 1.00
TokyoTintFadeInUDS       = 50
TokyoTintFadeOutExtraUDS = 140
TokyoTintDawnLeadUDS     = 240
TokyoTintOrangeStrong    = {1.00, 0.36, 0.14, 1.0} -- 強烈橘色
TokyoTintOrangeSoft      = {1.00, 0.55, 0.22, 1.0} -- 柔和橘色
TokyoTintRedStrong       = {0.92, 0.16, 0.16, 1.0} -- 強烈紅色

-- 用戶覆蓋 (保留參數)
OVERRIDE_DAWN_TOD      = 600   
OVERRIDE_DUSK_TOD      = 1900  
OVERRIDE_WINDOW_TOD    = 150
TransitionWindowTOD    = OVERRIDE_WINDOW_TOD
OVERRIDE_SLOW_SPEED    = 15
TransitionSlowSpeed    = OVERRIDE_SLOW_SPEED
ForceReloadStateOnCourseEnter = true -- [設定] 進入賽道時強制讀取上次狀態

-- 動態雲霧設置 (自動飄動與變化)
CloudAutoEnabled        = true
CloudMin                = 0.8
CloudMax                = 7.0
CloudDriftAmplitude     = 0.35
CloudSmoothingSeconds   = 3.5
CloudUpdateMs           = 125
CloudDriftPeriodSec     = 45.0
CloudMicroJitterAmp     = 0.08
CloudMicroJitterPeriodSec = 12.0

FogAutoEnabled          = true
FogMin                  = 0.0
FogMax                  = 6.5
FogDriftAmplitude       = 0.45
FogSmoothingSeconds     = 6.0
FogPhaseShift           = 0.65

DayMoodEnabled          = true -- [設定] 每日心情 (隨機天氣變化)
MoodCloudScale          = 1.2
MoodFogScale            = 0.8
MoodSmoothingSeconds    = 30.0
PresetTransitionUDS     = 120

-- 早晨配置文件
MorningProfilesEnabled       = true      
MorningStartOffsetUDS        = 0         
MorningEndOffsetUDS          = 300       
MorningBlendEdgeUDS          = 120       
MorningProfileWeights        = { clear=0.25, partial=0.50, overcast=0.25 } 
ResumeRandomizeAfterMorning  = true

-- 運行時變量 (請勿修改)
_MorningStartTOD   = 600
_MorningEndTOD     = 900
_MorningProfile    = "partial"
_MorningWasActive  = false
_MoodTarget        = 0.0
_MoodCurrent       = 0.0
_TokyoTintWasActive = false
_TokyoTintLastFactorLogged = 0.0
_LastWorldTag = "unknown"

_TransitionActive   = false
_TransitionReason   = "none"
_TransitionLastFlip = os.clock()
_TransitionHoldLeft = 0.0
_PrevSimSpeed       = nil
_PrevTimeSpeed      = nil

_ReassertLeft       = nil    
_SavedTimeSpeed     = nil    
_RestoredTOD        = nil    

MaxPresetGapAfterReassertUDS = 800  
_PresetScheduleDeferred      = false

-- 持久化與加載 (存檔系統)
PersistentStateEnabled  = true  -- [設定] 啟用狀態存檔
AutosaveSeconds         = 5.0   -- [設定] 自動存檔間隔
ReassertAtLoadSeconds   = 3.0   -- [設定] 載入時確認時間
FreezeDuringReassert    = true
ReassertMaxDelta        = 120.0

-- 手動控制狀態
_ManualControlActive    = false

-- =============== 日誌系統 (LOGGING SYSTEM) ===============
LOG_ENABLED = true

-- [函數] 獲取 Mod 根目錄
local function _GetModRoot()
  local info = debug.getinfo(1, "S")
  local src = info and info.source or ""
  if src:sub(1,1) == "@" then src = src:sub(2) end
  src = src:gsub("\\", "/")
  local dir = src:match("^(.*)/[^/]+$") or "."
  dir = dir:gsub("/Scripts$", "")
  local function is_abs(p) return p:match("^%a:[/\\]") or p:sub(1,2)=="\\\\" end
  if not is_abs(dir) then
    local cwd = nil
    pcall(function()
      local f = io.popen("cd")
      if f then cwd = (f:read("*l") or ""):gsub("/","\\"); f:close() end
    end)
    if cwd and cwd ~= "" then
      if not dir:lower():find("mods/") and not dir:lower():find("mods\\") then
        dir = cwd .. "\\Mods\\" .. dir
      else
        dir = cwd .. "\\" .. dir
      end
    end
  end
  dir = dir:gsub("/","\\")
  return dir
end

-- [函數] 確保目錄存在
local function _EnsureDir(path)
  pcall(function()
    os.execute('if not exist "'..path..'" mkdir "'..path..'"')
  end)
end
local LOG_DIR = _GetModRoot() .. "\\Logs"
_EnsureDir(LOG_DIR)

local _log_file, _log_path, _log_lastflush = nil, nil, 0.0
local function _NowStr()
  local t = os.date("*t")
  return string.format("%04d-%02d-%02d_%02d-%02d-%02d", t.year,t.month,t.day,t.hour,t.min,t.sec)
end

local _LogToggleBusy = false
local _LogState = "off"
local _ManualLogToggle = false

-- [函數] 初始化日誌
local function LogInit()
  if _LogState ~= "opening" and _LogState ~= "off" then return end
  local ok = pcall(function()
    _EnsureDir(LOG_DIR)
    local fname = "txr_daynight_" .. _NowStr() .. ".log"
    _log_path = LOG_DIR .. "\\" .. fname
    if _log_file then
      pcall(function() _log_file:flush() end)
      pcall(function() _log_file:close() end)
      _log_file = nil
    end
    local fh, err = io.open(_log_path, "w")
    if not fh then
      print("TXR Day/Night: log open failed: "..tostring(err))
      return
    end
    _log_file = fh
    _log_file:write("[INFO] log start: ", os.date(), "\n")
    _log_file:flush()
    print("TXR Day/Night: logging to " .. _log_path)
  end)
  if not ok then
    print("TXR Day/Night: LogInit() failed (pcall)")
    _log_file = nil
    LOG_ENABLED = false
    _LogState = "off"
  else
    _LogState = "on"
  end
end

-- [函數] 關閉日誌
local function LogClose()
  if _LogState ~= "closing" and _LogState ~= "on" then return end
  local ok = pcall(function()
    if _log_file then
      pcall(function() _log_file:write("[INFO] log closed: ", os.date(), "\n") end)
      pcall(function() _log_file:flush() end)
      pcall(function() _log_file:close() end)
      _log_file = nil
    end
  end)
  if not ok then
    print("TXR Day/Night: LogClose() failed (pcall)")
  end
  _LogState = "off"
end

-- [函數] 寫入日誌
local function Log(fmt, ...)
  if not LOG_ENABLED or _LogState ~= "on" then return end
  if not _log_file then return end
  local ok, line = pcall(string.format, fmt, ...)
  if not ok then line = tostring(fmt) end
  pcall(function()
    _log_file:write(string.format("[%s] %s\n", os.date("%H:%M:%S"), line))
  end)
  local t = os.clock()
  if t - _log_lastflush > 0.5 then
    _log_lastflush = t
    pcall(function() _log_file:flush() end)
  end
end

local function Info(fmt, ...)
  local ok, line = pcall(string.format, fmt, ...)
  if not ok then line = tostring(fmt) end
  if VerboseConsole then
    print("[TXR] "..line)
  end
  Log("%s", line)
end

-- [函數] 啟用/停用日誌
local function EnableLogging()
  if LOG_ENABLED and _LogState == "on" then return end
  _LogState = "opening"
  LOG_ENABLED = true
  LogInit()
  if _LogState == "on" then
    Log("== logging enabled ==")
    pcall(function() print("TXR Day/Night: logging ON -> "..tostring(LOG_DIR)) end)
  else
    LOG_ENABLED = false
  end
end
if AutoEnableLogging and _LogState == "off" then pcall(function() EnableLogging() end) end

local function DisableLogging()
  if not LOG_ENABLED and _LogState == "off" then return end
  if LOG_ENABLED and _LogState == "on" then
    Log("== logging disabled ==")
  end
  _LogState = "closing"
  LogClose()
  LOG_ENABLED = false
  pcall(function() print("TXR Day/Night: logging OFF") end)
end

-- ===== Keybind wrapper (安全按鍵綁定) =====
local function SafeRegisterKeyBind(key, mods, fn)
  local bound = false
  if type(RegisterKeyBind) == "function" then
    local ok = pcall(function() RegisterKeyBind(key, mods or {}, fn); bound = true end)
    if not ok then
      pcall(function() RegisterKeyBind(key, mods or {}); bound = true end)
    end
  end
end

-- ================= PERSISTENT SNAPSHOT (狀態存檔) =====================
local SavedTimeOfDay, SavedCloudCoverage, SavedFog = nil, nil, nil
local SavedSlowActive, SavedSlowReason, SavedSlowStart, SavedSlowEnd = nil, nil, nil, nil
local _ResumeSlowPending = false
local STATE_FILE = _GetModRoot() .. "\\last_state.txt"

-- [函數] 寫入狀態到檔案
local function _WriteState(tod, cc, fog, reason, slow_active, slow_reason, slow_start, slow_end)
  local f = io.open(STATE_FILE, "w")
  if f then
    f:write(string.format(
      "tod=%.6f,cloud=%.6f,fog=%.6f,slow=%d,slow_reason=%s,slow_start=%.6f,slow_end=%.6f\n",
      tonumber(tod or -1) or -1,
      tonumber(cc or -1) or -1,
      tonumber(fog or -1) or -1,
      (slow_active and 1 or 0),
      tostring(slow_reason or "none"),
      tonumber(slow_start or -1) or -1,
      tonumber(slow_end or -1) or -1
    ))
    f:close()
  end
end

-- [函數] 從檔案讀取狀態
local function _ReadStateFile()
  local f = io.open(STATE_FILE, "r"); if not f then return nil,nil,nil,nil,nil,nil,nil end
  local s = f:read("*a") or ""; f:close()
  local t  = tonumber((s:match("tod=([%-%d%.eE]+)"))) or nil
  local c  = tonumber((s:match("cloud=([%-%d%.eE]+)"))) or nil
  local g  = tonumber((s:match("fog=([%-%d%.eE]+)"))) or nil
  local sa = tonumber((s:match("slow=([01])"))) or nil
  local sr = (s:match("slow_reason=([%a_]+)")) or nil
  local ss = tonumber((s:match("slow_start=([%-%d%.eE]+)"))) or nil
  local se = tonumber((s:match("slow_end=([%-%d%.eE]+)"))) or nil
  return t,c,g,sa,sr,ss,se
end

local function _AnyValid(t,c,g)
  return (t and t>=0 and t<=2400) or (c and c>=0) or (g ~= nil and g>=0)
end

-- [函數] 載入狀態快照
function LoadStateSnapshot()
  if not PersistentStateEnabled then return false end
  local t,c,g,sa,sr,ss,se = _ReadStateFile()
  if t and t >= 0 and t <= 2400 then SavedTimeOfDay = t end
  if c and c >= 0 then SavedCloudCoverage = c end
  if g ~= nil then SavedFog = g end
  if sa ~= nil then SavedSlowActive = (sa == 1) end
  if sr then      SavedSlowReason = sr end
  if ss then      SavedSlowStart  = ss end
  if se then      SavedSlowEnd    = se end
  return (SavedTimeOfDay ~= nil) or (SavedCloudCoverage ~= nil) or (SavedFog ~= nil) or (SavedSlowActive ~= nil)
end

-- [函數] 儲存狀態快照
function SaveStateSnapshot(reason)
  if not PersistentStateEnabled then return end
  local tod = SavedTimeOfDay
  if CachedCourseSkyActor and CachedCourseSkyActor:IsValid() then
    tod = CachedCourseSkyActor['Time Of Day'] or tod
  end
  local w = (type(GetWeather)=="function") and GetWeather() or nil
  local cc = SavedCloudCoverage
  local fog = SavedFog
  if w then
    cc = w['Cloud Coverage'] or cc
    fog = w['Fog'] or fog
  end
  local pt, pc, pg = _ReadStateFile()
  if not tod or tod < 0 or tod > 2400 then tod = pt end
  if not cc or cc < 0 then cc = pc end
  if fog == nil then fog = pg end
  if not _AnyValid(tod, cc, fog) then return end
  if tod then tod = math.max(0, math.min(2400, tod)) else tod = -1 end
  if cc  then cc  = math.max(0, cc) else cc = -1 end
  if fog == nil then fog = -1 end

  local sActive, sReason, sStart, sEnd = false, "none", -1, -1
  local curTOD = tod
  if CachedCourseSkyActor and CachedCourseSkyActor:IsValid() then
    curTOD = CachedCourseSkyActor['Time Of Day'] or tod
  end
  local inDawn = (curTOD and curTOD >= SlowWindowDawnStart and curTOD <= SlowWindowDawnEnd)
  local inDusk = (curTOD and curTOD >= SlowWindowDuskStart and curTOD <= SlowWindowDuskEnd)
  if (_TransitionActive or inDawn or inDusk) then
    sActive = true
    sReason = (_TransitionReason ~= "none") and _TransitionReason or (inDawn and "dawn" or "dusk")
    if sReason == "dawn" then sStart, sEnd = SlowWindowDawnStart, SlowWindowDawnEnd else sStart, sEnd = SlowWindowDuskStart, SlowWindowDuskEnd end
  end
  _WriteState(tod, cc, fog, reason, sActive, sReason, sStart, sEnd)
end
-- ================ HELPERS (輔助函數) ======================
-- [函數] 嘗試獲取 Sky 類別
local function TryGetSkyClass()
  if not SkyClass or not SkyClass:IsValid() then
    SkyClass = StaticFindObject('/Game/ITSB/ArtAssets/Models/Course/ACOMMON/Sky/BP_Sky.BP_Sky_C')
  end
  return SkyClass
end

local function TryGetCourseSkyClass()
  if not CourseSkyClass or not CourseSkyClass:IsValid() then
    CourseSkyClass = StaticFindObject('/Game/ITSB/ArtAssets/Models/Course/ACOMMON/Sky/BP_CourseSky.BP_CourseSky_C')
  end
  return CourseSkyClass
end

-- [函數] 獲取天氣物件
function GetWeather()
  if not CachedCourseSkyActor or not CachedCourseSkyActor:IsValid() then return nil end
  local w = CachedCourseSkyActor['Ultra Dynamic Weather']
  if w and w.IsValid and w:IsValid() then return w end
  w = CachedCourseSkyActor['UltraDynamicWeather']
  if w and w.IsValid and w:IsValid() then return w end
  return nil
end

local function Clamp(x, a, b)
  if x < a then return a end
  if x > b then return b end
  return x
end

local function Lerp(a,b,alpha) return a + (b-a)*alpha end

local function FracDay()
  local tod = 1200
  if CachedCourseSkyActor and CachedCourseSkyActor:IsValid() then
    tod = CachedCourseSkyActor['Time Of Day'] or 1200
  end
  tod = Clamp(tod, 0, 2400)
  return tod / 2400.0
end

local function _LerpColor(a,b,t)
  t = Clamp(t,0,1)
  return {
    (a[1] or 0)+(b[1]-a[1])*t,
    (a[2] or 0)+(b[2]-a[2])*t,
    (a[3] or 0)+(b[3]-a[3])*t,
    (a[4] or 1)+(b[4]-a[4])*t
  }
end

-- [函數] 計算晨昏因子
local function _DawnDuskFactor(tod)
  local function s01(x) if x<=0 then return 0 elseif x>=1 then return 1 else return x*x*(3-2*x) end end
  local inDawn = (tod >= SlowWindowDawnStart - 60) and (tod <= SlowWindowDawnEnd + 60)
  local inDusk = (tod >= SlowWindowDuskStart - 60) and (tod <= SlowWindowDuskEnd + 60)
  if inDawn then
    local fin  = s01((tod - (SlowWindowDawnStart - 60))/120)
    local fout = s01(((SlowWindowDawnEnd + 60) - tod)/120)
    return Clamp(fin*fout,0,1)
  elseif inDusk then
    local fin  = s01((tod - (SlowWindowDuskStart - 60))/120)
    local fout = s01(((SlowWindowDuskEnd + 60) - tod)/120)
    return Clamp(fin*fout,0,1)
  end
  return 0
end

-- [函數] 計算東京色調
local function _TokyoTintCompute(tod)
  local dawn_in_start   = SlowWindowDawnStart - TokyoTintDawnLeadUDS
  local dawn_full_end   = SlowWindowDawnEnd
  local dawn_out_end    = SlowWindowDawnEnd + TokyoTintFadeOutExtraUDS
  local dusk_in_start   = SlowWindowDuskStart - TokyoTintFadeInUDS
  local dusk_full_end   = SlowWindowDuskEnd
  local dusk_out_end    = SlowWindowDuskEnd + TokyoTintFadeOutExtraUDS
  local function s01(x) if x<=0 then return 0 elseif x>=1 then return 1 else return x*x*(3-2*x) end end

  if tod >= dawn_in_start and tod <= dawn_out_end then
    local fin  = s01((tod - dawn_in_start) / math.max(TokyoTintDawnLeadUDS, 0.0001))
    local fout = s01((dawn_out_end - tod) / math.max(TokyoTintFadeOutExtraUDS, 0.0001))
    local factor = Clamp(fin * fout, 0, TokyoTintMaxStrength)
    local phase = Clamp((tod - SlowWindowDawnStart) / math.max(dawn_full_end - SlowWindowDawnStart, 1), 0, 1)
    local warm  = _LerpColor(TokyoTintOrangeStrong, TokyoTintOrangeSoft, phase)
    return factor, warm, "dawn"
  end

  if tod >= dusk_in_start and tod <= dusk_out_end then
    local fin  = s01((tod - dusk_in_start) / math.max(TokyoTintFadeInUDS, 0.0001))
    local fout = s01((dusk_out_end - tod) / math.max(TokyoTintFadeOutExtraUDS, 0.0001))
    local factor = Clamp(fin * fout, 0, TokyoTintMaxStrength)
    local phase = Clamp((tod - SlowWindowDuskStart) / math.max(dusk_full_end - SlowWindowDuskStart, 1), 0, 1)
    local warm  = _LerpColor(TokyoTintOrangeStrong, TokyoTintRedStrong, phase)
    return factor, warm, "dusk"
  end
  return 0, nil, "none"
end

local function _Smooth01(x) if x<=0 then return 0 elseif x>=1 then return 1 else return x*x*(3-2*x) end end
local function _MorningFactor(tod)
  local s = _MorningStartTOD
  local e = _MorningEndTOD
  if not s or not e or e <= s then return 0 end
  local edge = MorningBlendEdgeUDS or 60
  if edge <= 0 then edge = 60 end
  local fin  = _Smooth01((tod - s)/edge)
  local fout = _Smooth01((e - tod)/edge)
  return Clamp(fin * fout, 0, 1)
end

local function _PickMorningProfile()
  local r = math.random()
  local acc = 0
  for k,v in pairs(MorningProfileWeights) do
    acc = acc + (v or 0)
    if r <= acc then return k end
  end
  return "partial"
end

local function _reset_tokyo_morning_if_new_day(frac)
  if _LastFrac and frac < _LastFrac then
    _TokyoMorningInjected = false
  end
end

local function _maybe_inject_tokyo_morning(TOD)
  if not PresetAutoEnabled then return false end
  if _TokyoMorningInjected then return false end
  if not WeatherPresets or not WeatherPresets.tokyo_morning then return false end
  local windowStart = (SlowWindowDawnStart - 90) 
  if windowStart < 0 then windowStart = windowStart + 2400 end
  local windowEnd   = SlowWindowDawnStart + 30   
  if TOD >= windowStart and TOD <= windowEnd then
    local def = WeatherPresets.tokyo_morning
    local dur = 100
    if def and def.dur_uds_range then
      local minv, maxv = def.dur_uds_range[1], def.dur_uds_range[2]
      if minv and maxv then dur = minv + (maxv - minv) * math.random() end
    end
    apply_preset('tokyo_morning', dur)
    _TokyoMorningInjected = true
    return true
  end
  return false
end

-- Headlights helpers (車燈輔助)
local EKeys = nil
pcall(function() EKeys = require("Enums.EKeys") end)

local _HL_On                 = false
local _HL_CheckAccum         = 0.0
local _HL_CheckEverySeconds  = 0.25
local _HL_CachedPawn         = nil
local _HL_CachedGarageMgr    = nil
local _HL_ManualOverride     = false 
local _HL_ComboHeld          = false 

local function _GetPlayerController()
  local pc = FindFirstOf("PlayerController")
  if pc and pc.IsValid and pc:IsValid() then return pc end
  return nil
end

local function _GetPlayerPawn()
  local pc = _GetPlayerController()
  local pawn = pc and pc.Pawn or nil
  if pawn and pawn.IsValid and pawn:IsValid() then return pawn end
  return nil
end

local function _FindGarageManager()
  local gm=nil; pcall(function() gm=FindFirstOf("BP_OutGameGarageManager_C") end)
  if gm and gm.IsValid and gm:IsValid() then return gm end
  return nil
end

local function _SetVehicleLights(obj, on)
  if not obj or not (obj.IsValid and obj:IsValid()) then return end
  pcall(function() obj:SetLightOn(on) end)
  if on then pcall(function() obj:SetLightSpriteScale(0) end) end
end

local function _ApplyHeadlights_Course(shouldOn)
  local pawn = _HL_CachedPawn or _GetPlayerPawn()
  if pawn and pawn.IsValid and pawn:IsValid() then _HL_CachedPawn = pawn else return end
  _SetVehicleLights(pawn, shouldOn)
end

local function _ApplyHeadlights_Garage(shouldOn)
  local gm = _HL_CachedGarageMgr or _FindGarageManager()
  if gm and gm.IsValid and gm:IsValid() then _HL_CachedGarageMgr = gm else return end
  local vehicle = {}; local ok=false
  pcall(function() gm:GetDisplayVehicle(vehicle); ok=true end)
  if not ok then return end
  local v = vehicle.out_vehicle
  if v and v.IsValid and v:IsValid() then
    _SetVehicleLights(v, shouldOn)
  end
end

local function _ShouldHeadlightsOn_THR(TOD)
  if not TOD then return false end
  return (TOD >= HeadlightOnThresholdTOD) or (TOD < HeadlightOffThresholdTOD)
end

-- ===================== WEATHER PRESETS (天氣預設集) =====================
PresetAutoEnabled = true
_PresetActive      = false
_PresetName        = nil
_PresetStartTOD    = nil
_PresetDurationUDS = 0
_PresetEndTOD      = nil
_PresetTargets     = nil
_NextPresetTOD     = nil
_NextPresetName    = nil
_NextPresetDurUDS  = nil

-- [函數] 清除當前預設 (正式對齊版)
function clear_preset()
  if not _PresetActive then return end
  
  -- 1. 調用 TXRLog 工具顯示整齊的訊息 (使用 6 格縮進格式)
  TXRLog("TXR WEATHER: OFF", {
    "Previous : " .. tostring(_PresetName), -- 顯示剛結束的天氣名稱
    "Status   : RETURNING TO DEFAULT"       -- 狀態：返回預設環境
  })

  -- 2. 執行清除邏輯
  _PresetActive = false
  local w = GetWeather()
  if w then
    -- 清除降雨/閃電數值 (使用 UDS 原生標籤)
    _WSetScalar(w, {'Rain','Rain Amount','RainIntensity','Rain Intensity','Precipitation','Precipitation Amount'}, 0.0)
    _WSetScalar(w, {'Lightning Intensity','LightningStrength','Lightning Frequency','Lightning Chance','Thunderstorm','Thunderstorm Intensity'}, 0.0)
  end
  
  -- 關閉特效粒子並開始雲量過渡
  _UDWDisableParticles()
  _StartCloudFogBlend(nil, nil, "clear:"..tostring(_PresetName))
  
  -- 3. 清空變數內容 (防止邏輯重複觸發)
  _PresetName, _PresetTargets, _PresetStartTOD, _PresetEndTOD = nil, nil, nil, nil
  _PresetDurationUDS = 0
end

local function _rand(minv, maxv) return minv + (maxv - minv) * math.random() end
local function _pickWeighted(tbl)
  local total=0; for _,e in ipairs(tbl) do total = total + (e.w or 0) end
  local r = math.random() * total
  local acc=0
  for _,e in ipairs(tbl) do acc = acc + (e.w or 0); if r <= acc then return e.name end end
  return tbl[1].name
end

local function _withinTODWindow(tod, target, tol)
  local d = (tod - target) % 2400
  if d > 1200 then d = 2400 - d end
  return d <= (tol or 5.0)
end

local function _WSetScalar(w, keys, val)
  if not w or not (w.IsValid and w:IsValid()) then return false, nil end
  for _,k in ipairs(keys) do
    local ok = pcall(function() w[k] = val end)
    if ok then return true, k end
  end
  return false, nil
end

local function withUDW(func)
  local udw = FindFirstOf("BP_CourseWeather_C") or FindFirstOf("Ultra_Dynamic_Weather_C")
  if not (udw and udw.IsValid and udw:IsValid()) then return end
  pcall(func, udw)
end
local function _safeCallUDW(udw, name)
  if udw and udw[name] then pcall(function() udw[name](udw) end) end
end
local _UDWAssetMap = {
  clear             = "Clear_Skies",
  partly_cloudy     = "Cloudy",
  cloudy            = "Cloudy",
  overcast          = "Overcast",
  foggy             = "Foggy",
  rain_light        = "Rain",
  rain              = "Rain",
  rain_thunderstorm = "Rain_Thunderstorm",
  tokyo_morning     = "Clear_Skies",
}

local _UDWParticlesWarmed = false
local function _UDWWarmupAll(udw)
  if not udw or not (udw.IsValid and udw:IsValid()) then return end
  _safeCallUDW(udw, "Construct All Weather State Objects")
  _safeCallUDW(udw, "Weather Startup Functions")
  _safeCallUDW(udw, "Warm Up Niagara Systems")
  _safeCallUDW(udw, "Make Rain Component")
  _safeCallUDW(udw, "Make Snow Component")
  _safeCallUDW(udw, "Make Dust Component")
  pcall(function()
    if udw["Warm Up Weather Particles On Begin Play"] ~= nil then
      udw["Warm Up Weather Particles On Begin Play"] = true
    end
  end)
end

local function _ForceRainFX(udw, lightning)
  if not udw or not (udw.IsValid and udw:IsValid()) then return end
  _safeCallUDW(udw, "Make Rain Component")
  pcall(function() udw["Enable Rain Particles"] = true end)
  if lightning then
    pcall(function() udw["Enable Lightning"] = true end)
    pcall(function() udw["Enable Lightning Particles"] = true end)
    if udw["Thunderstorm"] ~= nil then udw["Thunderstorm"] = true end
  end
  if udw["Refresh Settings"] ~= nil then udw["Refresh Settings"] = true end
end

local function _ForceRainOff(udw)
  if not udw or not (udw.IsValid and udw:IsValid()) then return end
  pcall(function() udw["Enable Rain Particles"] = false end)
  pcall(function() udw["Enable Lightning"] = false end)
  pcall(function() udw["Enable Lightning Particles"] = false end)
  if udw["Thunderstorm"] ~= nil then udw["Thunderstorm"] = false end
  if udw["Refresh Settings"] ~= nil then udw["Refresh Settings"] = true end
end

local function _UDWUpdateParticles(realized)
  withUDW(function(udw)
    local r = (realized and realized.rain) or 0
    local wind = (realized and realized.wind) or 0
    local lightningOn = (realized and realized.lightning) == true and ((realized.lightning_intensity or 0) > 0.05)
    local wantRain = r and r > 0.05
    local wantDust = (not wantRain) and wind > 0.55

    if (wantRain or lightningOn or wantDust) and not _UDWParticlesWarmed then
      _UDWWarmupAll(udw)
      _UDWParticlesWarmed = true
    end
    if wantRain then
      _ForceRainFX(udw, lightningOn)
    else
      _ForceRainOff(udw) 
    end
    udw["Enable Rain Particles"]  = wantRain
    udw["Enable Snow Particles"]  = false
    udw["Enable Dust Particles"]  = wantDust
    if not wantRain and wantDust then _safeCallUDW(udw, "Make Dust Component") end
    pcall(function() udw["Enable Lightning Particles"] = lightningOn end)
    pcall(function() udw["Enable Lightning"] = lightningOn end)
    if udw["Thunderstorm"] ~= nil then udw["Thunderstorm"] = lightningOn end
    if udw["Refresh Settings"] ~= nil then udw["Refresh Settings"] = true end
  end)
end

local function _UDWDisableParticles()
  withUDW(function(udw)
    udw["Enable Rain Particles"] = false
    udw["Enable Snow Particles"] = false
    udw["Enable Dust Particles"] = false
    pcall(function() udw["Enable Lightning Particles"] = false end)
    pcall(function() udw["Enable Lightning"] = false end)
  pcall(function() if udw["Thunderstorm"] ~= nil then udw["Thunderstorm"] = false end end)
  local zeroKeys = {
    "Rain","Rain Amount","RainIntensity","Rain Intensity","Precipitation","Precipitation Amount",
    "Lightning Intensity","LightningStrength","Lightning Frequency","Lightning Chance","Thunderstorm Intensity"
  }
  for _,k in ipairs(zeroKeys) do
    pcall(function() if udw[k] ~= nil then udw[k] = 0.0 end end)
  end
  _ForceRainOff(udw)
    _safeCallUDW(udw, "Make Rain Component")
    if udw["Refresh Settings"] ~= nil then udw["Refresh Settings"] = true end
  end)
end

local function _UDWForceDry(udw)
  if not udw or not (udw.IsValid and udw:IsValid()) then return end
  pcall(function() udw["Enable Rain Particles"] = false end)
  pcall(function() udw["Enable Lightning Particles"] = false end)
  pcall(function() udw["Enable Lightning"] = false end)
  pcall(function() if udw["Thunderstorm"] ~= nil then udw["Thunderstorm"] = false end end)
  local zeroKeys = {
    "Rain","Rain Amount","RainIntensity","Rain Intensity","Precipitation","Precipitation Amount",
    "Lightning Intensity","LightningStrength","Lightning Frequency","Lightning Chance","Thunderstorm Intensity"
  }
  for _,k in ipairs(zeroKeys) do
    pcall(function() if udw[k] ~= nil then udw[k] = 0.0 end end)
  end
  _ForceRainOff(udw)
  _safeCallUDW(udw, "Make Rain Component")
  if udw["Refresh Settings"] ~= nil then udw["Refresh Settings"] = true end
end

local function _ApplyUDWAsset(presetKey)
  local assetName = _UDWAssetMap[presetKey]; if not assetName then return end
  local path = string.format("/Game/UltraDynamicSky/Blueprints/Weather_Effects/Weather_Presets/%s.%s", assetName, assetName)
  local preset = StaticFindObject(path); if not (preset and preset.IsValid and preset:IsValid()) then
    return
  end
  withUDW(function(udw)
    _UDWWarmupAll(udw)
    udw.Weather = preset
    local isRain = tostring(presetKey):find("rain") ~= nil
    local isStorm = tostring(presetKey):find("thunder") ~= nil
    if isRain then
      _ForceRainFX(udw, isStorm)
    else
      _ForceRainOff(udw) 
    end
    if udw["Refresh Settings"] ~= nil then udw["Refresh Settings"] = true end
  end)
end

local function _StartCloudFogBlend(to_cloud, to_fog, reason)
  local A = CachedCourseSkyActor
  if not (A and A.IsValid and A:IsValid()) then return end
  local w = GetWeather()
  local curC, curF = nil, nil
  if w then
    curC = w['Cloud Coverage']
    curF = w['Fog']
  end
  _BlendFromCloud = curC or _CloudLast or 0.0
  _BlendFromFog   = curF or _FogLast   or 0.0
  _BlendStartTOD  = A['Time Of Day'] or 0
  _BlendDurUDS    = PresetTransitionUDS
  _BlendActive    = true
  _BlendReason    = reason or "preset"
end

local function _ApplyPresetMPC(world, mpc)
  if not mpc then return end
  if mpc.AmbientFogColor then
    local c = mpc.AmbientFogColor
    _AmbientFogBase = {c[1], c[2] or c[1], c[3] or c[1], c[4] or 1}
  end
end

-- [配置] 天氣預設定義表
local WeatherPresets = {
  clear = {
    cloud_range={0.0,0.6}, fog_range={0.0,0.3}, rain_range={0.0,0.0}, lightning=false, wind_range={0.0,0.2},
    dur_uds_range={80,260},
    mpc={ ExtinctionScale=0.35, MS_Occlusion=0.0, MS_Contribution=1.2, AO=0.0, AmbientFogColor={0.48,0.58,0.68,1.0}, GlobalExt=0.02, GroundExt=0.035 }
  },
  partly_cloudy = {
    cloud_range={1.2,3.2}, fog_range={0.1,0.7}, rain_range={0.0,0.0}, lightning=false, wind_range={0.1,0.5},
    dur_uds_range={120,360},
    mpc={ ExtinctionScale=0.42, MS_Occlusion=0.0, MS_Contribution=1.1, AO=0.0, AmbientFogColor={0.46,0.56,0.66,1.0}, GlobalExt=0.025, GroundExt=0.040 }
  },
  cloudy = {
    cloud_range={4.5,7.0}, fog_range={0.3,1.0}, rain_range={0.0,0.0}, lightning=false, wind_range={0.2,0.6},
    dur_uds_range={140,400},
    mpc={ ExtinctionScale=0.48, MS_Occlusion=0.0, MS_Contribution=1.0, AO=0.0, AmbientFogColor={0.44,0.52,0.60,1.0}, GlobalExt=0.028, GroundExt=0.045 }
  },
  overcast = {
    cloud_range={7.5,9.5}, fog_range={0.6,1.8}, rain_range={0.0,0.1}, lightning=false, wind_range={0.3,0.8},
    dur_uds_range={120,360},
    mpc={ ExtinctionScale=0.55, MS_Occlusion=0.0, MS_Contribution=0.95, AO=0.0, AmbientFogColor={0.42,0.50,0.58,1.0}, GlobalExt=0.030, GroundExt=0.055 }
  },
  foggy = {
    cloud_range={1.0,4.0}, fog_range={6.0,9.0}, rain_range={0.0,0.1}, lightning=false, wind_range={0.0,0.3},
    dur_uds_range={80,240},
    mpc={ ExtinctionScale=0.60, MS_Occlusion=0.0, MS_Contribution=1.0, AO=0.0, AmbientFogColor={0.40,0.48,0.56,1.0}, GlobalExt=0.040, GroundExt=0.090 }
  },
  rain_light = {
    cloud_range={6.5,8.5}, fog_range={1.0,2.5}, rain_range={0.12,0.30}, lightning=false, wind_range={0.3,0.8},
    dur_uds_range={100,260},
    mpc={ ExtinctionScale=0.62, MS_Occlusion=0.0, MS_Contribution=0.95, AO=0.0, AmbientFogColor={0.40,0.46,0.54,1.0}, GlobalExt=0.045, GroundExt=0.070 }
  },
  rain = {
    cloud_range={7.2,9.2}, fog_range={1.5,3.5}, rain_range={0.50,0.80}, lightning=false, wind_range={0.4,1.0},
    dur_uds_range={100,300},
    mpc={ ExtinctionScale=0.66, MS_Occlusion=0.0, MS_Contribution=0.90, AO=0.0, AmbientFogColor={0.38,0.45,0.52,1.0}, GlobalExt=0.050, GroundExt=0.080 }
  },
  rain_thunderstorm = {
    cloud_range={8.0,10.0}, fog_range={1.5,4.0}, rain_range={0.70,1.00}, lightning=true, lightning_intensity_range={0.5,1.0}, wind_range={0.6,1.2},
    dur_uds_range={80,220},
    mpc={ ExtinctionScale=0.70, MS_Occlusion=0.0, MS_Contribution=0.85, AO=0.0, AmbientFogColor={0.34,0.40,0.48,1.0}, GlobalExt=0.055, GroundExt=0.090 }
  },
  tokyo_morning = { 
    cloud_range={0.8,2.6}, fog_range={0.4,1.3}, rain_range={0.0,0.0}, lightning=false, wind_range={0.0,0.4},
    dur_uds_range={80,140},
    mpc={ ExtinctionScale=0.50, MS_Occlusion=0.0, MS_Contribution=1.05, AO=0.0, AmbientFogColor={0.95,0.40,0.22,1.0}, GlobalExt=0.030, GroundExt=0.050 }
  },
}

local _RandomPresetWeights = {
  {name="clear",              w=1.0},
  {name="partly_cloudy",      w=1.0},
  {name="cloudy",             w=1.0},
  {name="overcast",           w=1.0},
  {name="foggy",              w=1.0},
  {name="rain_light",         w=1.0},
  {name="rain",               w=1.0},
  {name="rain_thunderstorm",  w=1.0},
}

local function _realizePreset(def)
  local t = {}
  if def.cloud_range then t.cloud = _rand(def.cloud_range[1], def.cloud_range[2]) end
  if def.fog_range   then t.fog   = _rand(def.fog_range[1],   def.fog_range[2])   end
  if def.rain_range  then t.rain  = _rand(def.rain_range[1],  def.rain_range[2])  else t.rain = 0.0 end
  if def.wind_range  then t.wind  = _rand(def.wind_range[1],  def.wind_range[2])  end
  if def.lightning ~= nil then t.lightning = def.lightning end
  if def.lightning_intensity_range then
    t.lightning_intensity = _rand(def.lightning_intensity_range[1], def.lightning_intensity_range[2])
  end
  if def.dur_uds_range then t.duration = _rand(def.dur_uds_range[1], def.dur_uds_range[2]) end
  t.mpc = def.mpc
  return t
end

local function _schedule_next_random(nowTOD)
  _NextPresetName = _pickWeighted(_RandomPresetWeights)
  local def = WeatherPresets[_NextPresetName]
  local temp = _realizePreset(def)
  _NextPresetDurUDS = temp.duration or _rand(80,360)
  local gap = _rand(30, 240)
  _NextPresetTOD = (nowTOD + gap) % 2400

  -- [TXR] 直觀時間轉換邏輯
  local wait_mins = math.floor(gap * 0.6)
  local dur_mins = math.floor(_NextPresetDurUDS * 0.6)
  local total_start_m = _NextPresetTOD * 0.6
  local start_time_str = string.format("%02d:%02d", math.floor(total_start_m / 60), math.floor(total_start_m % 60))

  -- 將原本的 print 替換為 TXRLog 顯示
  TXRLog("TXR WEATHER FORECAST", {
    "Next Style : " .. tostring(_NextPresetName),
    "Start At   : " .. start_time_str .. " (In " .. wait_mins .. " mins)",
    "Duration   : ~ " .. dur_mins .. " mins",
  })
end

local function _apply_precip_and_wind(w, realized)
  if not w or not (w.IsValid and w:IsValid()) then return end
  _WSetScalar(w, {'Rain','Rain Amount','RainIntensity','Rain Intensity','Precipitation','Precipitation Amount'}, realized.rain or 0.0)
  _WSetScalar(w, {'Wind Speed','Wind Strength','Wind Intensity'}, realized.wind or 0.0)
  if realized.lightning ~= nil then
    local on = (realized.lightning == true) and (realized.lightning_intensity or 0.6) or 0.0
    _WSetScalar(w, {'Lightning Intensity','LightningStrength','Lightning Frequency','Lightning Chance','Thunderstorm','Thunderstorm Intensity'}, on)
  end
end

-- [函數] 應用天氣預設 (整合強化雲速與 TXR 標準日誌)
function apply_preset(name, durationUDS)
  local def = WeatherPresets[name]
  if not def then print("apply_preset: unknown "..tostring(name)); return false end
  local A = CachedCourseSkyActor
  if not A or not (A.IsValid and A:IsValid()) then print("apply_preset: world not ready"); return false end
  
  local nowTOD = A['Time Of Day'] or 0
  local realized = _realizePreset(def)
  
  _PresetName        = name
  _PresetTargets     = realized
  _PresetStartTOD    = nowTOD
  _PresetDurationUDS = durationUDS or realized.duration or _rand(80,300)
  _PresetEndTOD      = (nowTOD + _PresetDurationUDS) % 2400
  _PresetActive      = true

  local w = GetWeather()
  if w then
    -- 1. 基礎覆蓋設定
    pcall(function() w['Cloud Coverage - Manual Override'] = true end)
    pcall(function() w['Fog - Manual Override'] = true end)
    
    -- 2. 【核心強化】雲層飄動速度控制 (Cloud Speed Control)
    pcall(function() w['Cloud Speed - Manual Override'] = true end)
    
    -- 根據天氣名稱動態調整速度
    local targetSpeed = 2.5 -- 預設速度 (晴天)
    if name:find("rain") or name:find("thunder") then
        targetSpeed = 6.5 -- 暴雨天雲飄極快 (更有暴風感)
    elseif name:find("overcast") or name:find("cloudy") then
        targetSpeed = 4.0 -- 陰天雲飄較快
    elseif name:find("fog") then
        targetSpeed = 1.2 -- 霧天雲飄較慢
    end
    w['Cloud Speed'] = targetSpeed
    
    _apply_precip_and_wind(w, realized)
  end

  -- 3. 地面與粒子效果處理
  local isDryTarget = (realized.rain or 0) <= 0.05 and not realized.lightning
  if isDryTarget then
    withUDW(function(udw) _UDWForceDry(udw) end) 
  else
    withUDW(function(udw) if not _UDWParticlesWarmed then _UDWWarmupAll(udw) end end)
  end

  local World = A:GetWorld()
  _ApplyPresetMPC(World, realized.mpc)
  _ApplyUDWAsset(_PresetName)
  _UDWUpdateParticles(realized)

  if isDryTarget then
    withUDW(function(udw) _UDWForceDry(udw) end)
  elseif (realized.rain or 0) > 0.05 or realized.lightning then
    withUDW(function(udw)
      if not _UDWParticlesWarmed then
        _UDWWarmupAll(udw); _ForceRainFX(udw, realized.lightning); _UDWParticlesWarmed=true;
      end
    end)
  end
  
  _StartCloudFogBlend(realized.cloud, realized.fog, "apply:"..tostring(name))

  -- 4. 【控制台輸出】採用 TXR 標準對齊格式
  local dur_mins = math.floor(_PresetDurationUDS * 0.6) 
  
  TXRLog("TXR WEATHER START", {
      "Preset : " .. tostring(name),
      "Period : " .. FormatToTime(_PresetStartTOD) .. " -> " .. FormatToTime(_PresetEndTOD),
      "Wind   : SPEED " .. string.format("%.1f", w and w['Cloud Speed'] or 0), -- 顯示風速
      "Length : ~ " .. dur_mins .. " mins",
      "Status : APPLYING DYNAMIC CLOUDS"
  })

  return true
end

-- [函數] 清除當前預設 (正式對齊版)
function clear_preset()
  if not _PresetActive then return end
  
  -- 暫存名稱用於最後顯示
  local oldName = tostring(_PresetName)
  
  -- 1. 執行清除邏輯
  _PresetActive = false
  local w = GetWeather()
  if w then
    -- 清除所有降雨與閃電數值
    _WSetScalar(w, {'Rain','Rain Amount','RainIntensity','Rain Intensity','Precipitation','Precipitation Amount'}, 0.0)
    _WSetScalar(w, {'Lightning Intensity','LightningStrength','Lightning Frequency','Lightning Chance','Thunderstorm','Thunderstorm Intensity'}, 0.0)
  end
  
  -- 關閉特效粒子並開始雲量與霧氣過渡回預設
  _UDWDisableParticles()
  _StartCloudFogBlend(nil, nil, "clear:"..oldName)
  
  -- 2. 使用 TXRLog 顯示整齊訊息 (對齊 6 格縮進)
  TXRLog("TXR WEATHER: OFF", {
    "Previous : " .. oldName,         -- 前一個結束的天氣名稱
    "Status   : SYSTEM RESTORED",     -- 狀態：系統已恢復預設
    "At TOD   : " .. FormatToTime(TOD) -- 結束時的遊戲時間
  })
  
  -- 3. 清空變數內容
  _PresetName, _PresetTargets, _PresetStartTOD, _PresetEndTOD = nil, nil, nil, nil
  _PresetDurationUDS = 0
end

local function apply_preset_random_now()
  local A = CachedCourseSkyActor; if not A or not (A.IsValid and A:IsValid()) then return end
  if not _PresetActive then
    local name = _pickWeighted(_RandomPresetWeights)
    local def  = WeatherPresets[name]
    local d    = (_realizePreset(def).duration or _rand(80,300))
    apply_preset(name, d)
  else
    clear_preset()
  end
end

local function _MorningBiases()
  if not MorningProfilesEnabled then return 0.0, 0.0 end
  local p = _MorningProfile or "partial"
  if p == "clear" then return -0.9, -0.25   
  elseif p == "partial" then return 0.4, 0.05     
  elseif p == "overcast" then return 1.3, 0.30     
  elseif p == "foggy" then return 0.2, 2.2      
  else return 0.0, 0.0 end
end

local function TargetCoverage()
  local f = FracDay()
  local s = 0.5 * (1.0 - math.cos(2.0 * math.pi * (f + 0.15)))
  local t = os.clock() - _CloudT0
  local drift = CloudDriftAmplitude * (0.5 * (1.0 - math.cos(2.0 * math.pi * (t / CloudDriftPeriodSec))))
  local micro = CloudMicroJitterAmp * math.sin(2.0 * math.pi * ((t / CloudMicroJitterPeriodSec) + 0.37))
  local tod = (CachedCourseSkyActor and CachedCourseSkyActor:IsValid()) and (CachedCourseSkyActor['Time Of Day'] or 1200) or 1200
  local dd  = _DawnDuskFactor(tod)
  if dd > 0.0001 then
    local drift2 = (0.45 * dd) * math.sin(2.0 * math.pi * (t / 12.0))
    local micro2 = (0.20 * dd) * math.sin(2.0 * math.pi * (t / 6.5) + 1.1)
    drift = drift + drift2
    micro = micro + micro2
  end
  local v = CloudMin + (CloudMax - CloudMin) * s + drift + micro
  if MorningProfilesEnabled and CachedCourseSkyActor and CachedCourseSkyActor:IsValid() then
    local mfac = _MorningFactor(tod)
    if mfac > 0.0001 then
      local cbias, _ = _MorningBiases()
      v = v + (cbias * mfac)
    end
  end
  return Clamp(v, 0, 10)
end

local function TargetFog()
  local f = FracDay()
  local s = 0.5 * (1.0 - math.cos(2.0 * math.pi * (f + FogPhaseShift)))
  local t = os.clock() - _CloudT0
  local drift = FogDriftAmplitude * (0.5 * (1.0 - math.cos(2.0 * math.pi * (t / 105.0))))
  local tod = (CachedCourseSkyActor and CachedCourseSkyActor:IsValid()) and (CachedCourseSkyActor['Time Of Day'] or 1200) or 1200
  local dd  = _DawnDuskFactor(tod)
  if dd > 0.0001 then
    drift = drift + 0.25 * dd
  end
  local v = FogMin + (FogMax - FogMin) * s + drift
  if MorningProfilesEnabled and CachedCourseSkyActor and CachedCourseSkyActor:IsValid() then
    local mfac = _MorningFactor(tod)
    if mfac > 0.0001 then
      local _, fbias = _MorningBiases()
      v = v + (fbias * mfac)
    end
  end
  return Clamp(v, 0, 10)
end

local function _GetWorldTagFromActor(actor)
  local name = ""
  local tag  = "course"
  if not actor or not (actor.IsValid and actor:IsValid()) then
    return name, "unknown"
  end
  local worldObj = nil
  pcall(function()
    if actor.GetWorld then
      worldObj = actor:GetWorld()
    end
  end)
  local okValid = false
  if worldObj and worldObj.IsValid then
    local ok, res = pcall(function() return worldObj:IsValid() end)
    okValid = ok and res == true
  end
  if okValid then
    local ws = nil
    pcall(function() ws = tostring(worldObj) end)
    if type(ws) == "string" then
      local lw = ws:lower()
      if lw:find("garage") or lw:find("outgame") or lw:find("ls_") then
        tag = "outgame"
      elseif lw:find("_pa") or lw:find("/pa") or lw:find(" pa ") then
        tag = "pa"
      end
    end
  else
    return name, "unknown"
  end
  return name, tag
end

-- ================ LIFECYCLE (生命週期掛鉤) =====================
RegisterLoadMapPreHook(function()
  if LOG_ENABLED then Log("== map unload ==") end
  if PersistentStateEnabled and (_WorldTag == "course" or _WorldTag == "pa") then
    SaveStateSnapshot("LoadMapPre")
  end
  if PersistentStateEnabled and (_WorldTag ~= "course" and _WorldTag ~= "pa") then
    SaveStateSnapshot("LoadMapPre_noncourse")
  end
  if AutoEnableLogging and not _ManualLogToggle then
    Log("AutoEnableLogging: keeping log file open across map unload")
  else
    if LOG_ENABLED then DisableLogging() else LogClose() end
  end
  _CloudLoopExit = true
  _LastFrac = nil
  CachedCourseSkyActor = CreateInvalidObject()
  SkyClass = CreateInvalidObject()
  CourseSkyClass = CreateInvalidObject()
  _LastWorldTag = _WorldTag
  _WorldTag = "unknown"
  _ManualControlActive = false 
end)

RegisterBeginPlayPreHook(function(ActorParam)
  if CachedCourseSkyActor and CachedCourseSkyActor:IsValid() then return end
  local Actor = ActorParam and ActorParam:get() or nil
  if not Actor or not Actor:IsValid() then return end
  local SkyCls = TryGetSkyClass(); if not SkyCls or not SkyCls:IsValid() then return end
  if not Actor:IsA(SkyCls) then return end
  local CourseSkyCls = TryGetCourseSkyClass(); if not CourseSkyCls or not CourseSkyCls:IsValid() then return end
  if not Actor:IsA(CourseSkyCls) then return end

  local wname, tag = _GetWorldTagFromActor(Actor)
  _LastWorldTag = _WorldTag
  _WorldTag = tag
  Log("BeginPlayPre: actor ok; world=%s tag=%s", tostring(wname), tostring(tag))
  CachedCourseSkyActor = Actor
  pcall(function() Actor['Instant Exposure Adjustment On Begin Play'] = false end)

  if tag == "pa" then
    if PersistentStateEnabled then LoadStateSnapshot() end
    local restoredTOD = SavedTimeOfDay
    if restoredTOD and restoredTOD >=0 and restoredTOD <=2400 then
      pcall(function() Actor['Time Of Day'] = restoredTOD end)
    end
    local w = GetWeather()
    if w then
      if SavedCloudCoverage ~= nil then pcall(function() w['Cloud Coverage'] = SavedCloudCoverage end) end
      if SavedFog ~= nil then pcall(function() w['Fog'] = SavedFog end) end
      pcall(function() w['Cloud Coverage - Manual Override'] = true end)
      pcall(function() w['Fog - Manual Override'] = true end)
    end
    if PersistentStateEnabled then SaveStateSnapshot("enter_pa_restore") end
    pcall(function() Actor['Simulate Real Sun'] = false end)
    pcall(function() Actor['Animate Time of Day'] = false end)
    pcall(function() Actor['Time Speed'] = 0.0 end)
    return
  end

  if tag ~= "course" then
    pcall(function() Actor['Simulate Real Sun'] = false end)
    pcall(function() Actor['Animate Time of Day'] = false end)
    pcall(function() Actor['Time Speed'] = 0.0 end)
    return
  end

  pcall(function() Actor['Animate Time of Day'] = true end)

  if UseSystemTime then
    pcall(function() Actor['Use System Time'] = true end)
    pcall(function() Actor['Apply Daylight Savings Time'] = false end)
    pcall(function() Actor['Time Zone'] = TimeZoneOffset end)
  else
    pcall(function() if OVERRIDE_DAWN_TOD then Actor['Dawn Time'] = OVERRIDE_DAWN_TOD end end)
    pcall(function() if OVERRIDE_DUSK_TOD then Actor['Dusk Time'] = OVERRIDE_DUSK_TOD end end)
    pcall(function() _RecomputeHeadlightThresholds() end)
    if PersistentStateEnabled and SavedTimeOfDay == nil and SavedCloudCoverage == nil and SavedFog == nil then
      LoadStateSnapshot()
    end
    if PersistentStateEnabled and ForceReloadStateOnCourseEnter then
      LoadStateSnapshot()
    end
    local initialTOD
    if SavedTimeOfDay ~= nil then
      initialTOD = SavedTimeOfDay
      pcall(function() Actor['Time Of Day'] = initialTOD end)
      SavedTimeOfDay = nil
    else
      initialTOD = 1800.0 + (math.random() * 1000.0)
      if initialTOD >= 2400.0 then initialTOD = initialTOD - 2400.0 end
      pcall(function() Actor['Time Of Day'] = initialTOD end)
    end
    pcall(function() Actor['Simulation Speed'] = SimulationSpeed end)
    _PrevTimeSpeed = Actor['Time Speed']
    pcall(function() Actor['Simulation Speed'] = SimulationSpeed end)

    if FreezeDuringReassert and ReassertAtLoadSeconds and ReassertAtLoadSeconds > 0 then
      _ReassertLeft = ReassertAtLoadSeconds
      _SavedTimeSpeed = Actor['Time Speed']
      _RestoredTOD = Actor['Time Of Day']
      if FreezeDuringReassert then
        pcall(function() Actor['Animate Time of Day'] = false end)
      end
    end
    _PrevTimeSpeed = Actor['Time Speed']
    pcall(function() Actor['Simulation Speed'] = SimulationSpeed end)

    local tod_now = Actor['Time Of Day'] or 0
    local inDawn = (tod_now >= SlowWindowDawnStart and tod_now <= SlowWindowDawnEnd)
    local inDusk = (tod_now >= SlowWindowDuskStart and tod_now <= SlowWindowDuskEnd)
    if (SavedSlowActive or _ResumeSlowPending) and (inDawn or inDusk) then
      if not _TransitionActive then
        _TransitionActive = true
        _TransitionReason = inDawn and 'dawn' or 'dusk'
        local okSim, curSim = pcall(function() return Actor['Simulation Speed'] end)
        _PrevSimSpeed = (okSim and curSim) or SimulationSpeed
        local okTS, curTS = pcall(function() return Actor['Time Speed'] end)
        if okTS then _PrevTimeSpeed = curTS end
        pcall(function() Actor['Simulation Speed'] = TransitionSlowSpeed end)
        if _PrevSimSpeed and _PrevSimSpeed > 0.01 then
          local curTS = CachedCourseSkyActor['Time Speed'] or BaselineTimeSpeed
          local scaled = math.max(0.01, curTS * (TransitionSlowSpeed / _PrevSimSpeed))
          pcall(function() Actor['Time Speed'] = scaled end)
        end
        _ResumeSlowPending = false
      end
    end
    local wantHL = _ShouldHeadlightsOn_THR(tod_now)
    if wantHL ~= _HL_On then
      _ApplyHeadlights_Course(wantHL)
      _HL_On = wantHL
    end
  end 

-- [修正] 隨機天氣初始化 - 採用 TXR 標準英文排版
local Weather = GetWeather() -- 獲取當前地圖的天氣控制器 (Get current map weather)
if Weather then
    -- 1. 執行隨機雲量邏輯 (Randomize Cloud Coverage)
    if RandomizeClouds then
        Weather['Cloud Coverage'] = (math.random()^2) * 10.0
        Weather['Cloud Coverage - Manual Override'] = true
    end

    -- 2. 執行隨機霧量邏輯 (Randomize Fog Density)
    if RandomizeFog then
        Weather['Fog'] = math.max(0.0, (math.random() * 30.0) - 20.0)
        Weather['Fog - Manual Override'] = true
    end

    -- 3. 恢復先前儲存的數值 (Restore saved values if exist)
    if SavedCloudCoverage ~= nil then 
        Weather['Cloud Coverage'] = SavedCloudCoverage
        SavedCloudCoverage = nil 
    end
    if SavedFog ~= nil then 
        Weather['Fog'] = SavedFog
        SavedFog = nil 
    end
    
    -- 確保手動覆蓋模式開啟
    Weather['Cloud Coverage - Manual Override'] = true
    Weather['Fog - Manual Override'] = true
    
    -- 4. 暫存數值用於顯示 (Cache values for logging)
    local currentCloud = Weather['Cloud Coverage'] or 0.0
    local currentFog = Weather['Fog'] or 0.0
    _CloudLast = currentCloud
    _FogLast   = currentFog

    -- 5. 【控制台輸出：採用 TXRLog 標準對齊格式】
    -- 使用全英文防止 CMD 亂碼，右側備註中文供你參考
    TXRLog("TXR WEATHER INIT", {
        "Cloud Detail : " .. string.format("%.2f", currentCloud), -- 雲量細節
        "Fog Density : " .. string.format("%.2f", currentFog),   -- 霧氣濃度
        "Game Time   : " .. FormatToTime(TOD),                  -- 當前遊戲時間 (24小時制)
        "Status      : INITIALIZED"                             -- 狀態：初始化完成
    })
end

  if PersistentStateEnabled and (SavedSlowActive == true) then
    _ResumeSlowPending = true
  end

  _CloudLoopExit = false
  _CloudT0 = os.clock()
  -- [主循環] 開始異步循環
  LoopAsync(CloudUpdateMs, function()
    if _CloudLoopExit then return true end
    ExecuteInGameThread(function()
      local function _TickBody()
        if _WorldTag == "course" then
          -- 處理車庫或其他區域邏輯
          if _WorldTag == "outgame" then
            local dt_local = math.max(CloudUpdateMs/1000.0, 0.001)
            _HL_CheckAccum = _HL_CheckAccum + dt_local
            if _HL_CheckAccum >= _HL_CheckEverySeconds then
              _HL_CheckAccum = 0.0
              if _HL_On then _ApplyHeadlights_Garage(false); _HL_On = false end
            end
          elseif _WorldTag == "pa" then
            -- PA 區域狀態保存邏輯
            local A_pa = CachedCourseSkyActor
            if A_pa and A_pa.IsValid and A_pa:IsValid() and PersistentStateEnabled then
              local tod_pa = A_pa['Time Of Day'] or -1
              local nowClock = os.clock()
              if not _LastPASaveClock then _LastPASaveClock = nowClock; SaveStateSnapshot("pa_interval")
              else
                if (nowClock - _LastPASaveClock) >= 5.0 then
                  SaveStateSnapshot("pa_interval")
                  _LastPASaveClock = nowClock
                elseif _LastPAStateTOD then
                  local diff = (tod_pa - _LastPAStateTOD) % 2400
                  if diff < 0 then diff = diff + 2400 end
                  if diff >= 25.0 then
                    SaveStateSnapshot("pa_interval_tod")
                    _LastPAStateTOD = tod_pa
                    _LastPASaveClock = nowClock
                  end
                end
                if not _LastPAStateTOD then _LastPAStateTOD = tod_pa end
              end
            end
            return
          end

          -- 獲取天氣與天色物件
          local w = GetWeather(); if not w then return end
          local dt = math.max(CloudUpdateMs / 1000.0, 0.001)
          local A = CachedCourseSkyActor
          local TOD, curTS = 0, BaselineTimeSpeed
          
          if A and A.IsValid and A:IsValid() then
            TOD = A['Time Of Day'] or 0
            pcall(function() curTS = math.abs(A['Time Speed'] or BaselineTimeSpeed) end)
          end
          
          local expectedUDS = math.max(0.0, SimulationSpeed * curTS * dt)
          local tolUDS = math.max(4.0, expectedUDS * 2.5)

          -- [修正] DAY-LOOP RESET 邏輯：確保 23:00 自動跳轉
          -- [終極優化版] DAY-LOOP 邏輯
          if DayLoopEnabled then
            local nowClock = os.clock()
            _LastResetTime = _LastResetTime or 0
            
            -- 檢查重置點
            if TOD >= DayLoopEnd and (nowClock - _LastResetTime > 2) then
                _LastResetTime = nowClock
                
                -- 先保存舊時間數值用於顯示
                local displayOldTOD = TOD
                
                -- 執行重置 (數值操作)
                TOD = DayLoopStart
                _ManualControlActive = false 

                -- 強制同步引擎 (使用 pcall 確保不崩潰)
                pcall(function() 
                    if A and A:IsValid() then 
                        A['Time Of Day'] = DayLoopStart 
                        if A.SnapToTime then A:SnapToTime(DayLoopStart) end 
                    end 
                end)

                -- 確保日誌在重置發生時立即輸出
                -- 注意：這裡 FormatToTime 會把 630 轉成 06:18
                TXRLog("TRX DAY-LOOP RESET", {
                    "From   : " .. FormatToTime(displayOldTOD),
                    "To     : " .. FormatToTime(DayLoopStart),
                    "Status : TIME RESET APPLIED"
                })
                
                -- 清除天氣緩存，強制重新預報
                _NextPresetTOD = nil
                _PresetActive = false
            end
          end

          -- 如果手動模式開啟，跳過自動天氣調度
          if _ManualControlActive then return end

          -- 自動天氣調度邏輯
          if PresetAutoEnabled then
            if not _PresetActive then
              -- 檢查是否需要排程下一個隨機天氣
              if _ReassertLeft and _ReassertLeft > 0 then
                if not _PresetScheduleDeferred then _PresetScheduleDeferred = true end
              else
                if _PresetScheduleDeferred then _PresetScheduleDeferred = false end
                if not _maybe_inject_tokyo_morning(TOD) then
                  if not _NextPresetTOD then
                    _schedule_next_random(TOD)
                  else
                    local dist = (_NextPresetTOD - TOD + 2400) % 2400
                    if dist > MaxPresetGapAfterReassertUDS then _schedule_next_random(TOD) end
                  end
                  if _NextPresetTOD and _withinTODWindow(TOD, _NextPresetTOD, tolUDS) then
                    apply_preset(_NextPresetName, _NextPresetDurUDS)
                    _NextPresetTOD, _NextPresetName, _NextPresetDurUDS = nil, nil, nil
                  end
                end
              end
            else
              -- 檢查當前天氣預設是否結束
              local elapsed = (TOD - (_PresetStartTOD or TOD)) % 2400
              if elapsed >= math.max(0, (_PresetDurationUDS or 0) - tolUDS) then
                
                -- [修正] WEATHER: ENDING 通知：改為符合 TXRLog 格式
                TXRLog("TXR WEATHER ENDING", {
                    "Type   : " .. tostring(_PresetName),
                    "At TOD : " .. FormatToTime(TOD),
                    "Status : CLEARING PRESET"
                })

                clear_preset()
                _schedule_next_random(TOD)
              end
            end
          end

          -- 以下維持原有 UDS 引擎混合同步邏輯 (雲量、霧氣、Tint等)
          if _ReassertLeft and _ReassertLeft > 0 and A and A:IsValid() then
            _ReassertLeft = _ReassertLeft - dt
            local curTOD = A['Time Of Day'] or _RestoredTOD
            if _RestoredTOD and math.abs(curTOD - _RestoredTOD) > ReassertMaxDelta then
              A['Time Of Day'] = _RestoredTOD
            end
            if _ReassertLeft <= 0 then
              if FreezeDuringReassert then
                A['Time Speed'] = _SavedTimeSpeed or A['Time Speed']
                A['Animate Time of Day'] = true
                if _TransitionActive and _TransitionReason ~= 'none' then
                  pcall(function() A['Simulation Speed'] = TransitionSlowSpeed end)
                  if _PrevSimSpeed and _PrevSimSpeed > 0.01 then
                    local curTS2 = A['Time Speed'] or BaselineTimeSpeed
                    local scaled = math.max(0.01, curTS2 * (TransitionSlowSpeed / _PrevSimSpeed))
                    pcall(function() A['Time Speed'] = scaled end)
                  end
                end
              end
            end
          end

          do
            local todNow = (A and A:IsValid()) and (A['Time Of Day'] or 1200) or 1200
            local mfacNow = _MorningFactor(todNow)
            if mfacNow > 0.001 then _MorningWasActive = true
            elseif _MorningWasActive then
              _MorningWasActive = false
              if ResumeRandomizeAfterMorning then _MoodTarget = (math.random()*2.0 - 1.0) end
            end
          end

          local f_day = FracDay()
          _reset_tokyo_morning_if_new_day(f_day)
          if _LastFrac and f_day < _LastFrac then
            _MoodTarget = (math.random()*2.0 - 1.0)
            if MorningProfilesEnabled then _MorningProfile = _PickMorningProfile() end
          end
          _LastFrac = f_day
          local alphaM = 1.0 - math.exp(-dt / math.max(MoodSmoothingSeconds, 0.001))
          _MoodCurrent = Lerp(_MoodCurrent, _MoodTarget, alphaM)

          if CloudAutoEnabled then
            local targetC = TargetCoverage()
            if DayMoodEnabled then targetC = targetC + (_MoodCurrent * MoodCloudScale) end
            if _PresetActive and _PresetTargets and _PresetTargets.cloud then targetC = _PresetTargets.cloud end
            targetC = Clamp(targetC, 0, 10)
            local blendFrac = 1.0
            if _BlendActive then
              local elapsedUDS = (TOD - (_BlendStartTOD or TOD)) % 2400
              blendFrac = Clamp(elapsedUDS / math.max(_BlendDurUDS or PresetTransitionUDS,0.0001),0,1)
              local function s01(x) return (x<=0) and 0 or ((x>=1) and 1 or (x*x*(3-2*x))) end
              blendFrac = s01(blendFrac)
              if _BlendFromCloud ~= nil then targetC = _BlendFromCloud + (targetC - _BlendFromCloud)*blendFrac end
            end
            local alphaC = 1.0 - math.exp(-dt / math.max(CloudSmoothingSeconds,0.001))
            _CloudLast = _CloudLast or w['Cloud Coverage'] or targetC
            local nextC = Lerp(_CloudLast, targetC, alphaC)
            w['Cloud Coverage'] = nextC; _CloudLast = nextC
            if _BlendActive and blendFrac >= 0.999 then
              _BlendActive=false; _BlendReason="none"; _BlendFromCloud,_BlendFromFog=nil,nil
            end
          end

          if FogAutoEnabled then
            local targetF = TargetFog()
              if DayMoodEnabled then targetF = targetF + (_MoodCurrent * MoodFogScale) end
              if _PresetActive and _PresetTargets and _PresetTargets.fog then targetF = _PresetTargets.fog end
              targetF = Clamp(targetF, 0, 10)
              if _BlendActive and _BlendFromFog ~= nil then
                local elapsedUDS = (TOD - (_BlendStartTOD or TOD)) % 2400
                local bfrac = Clamp(elapsedUDS / math.max(_BlendDurUDS or PresetTransitionUDS,0.0001),0,1)
                local function s01(x) return (x<=0) and 0 or ((x>=1) and 1 or (x*x*(3-2*x))) end
                bfrac = s01(bfrac)
                targetF = _BlendFromFog + (targetF - _BlendFromFog)*bfrac
              end
              local alphaF = 1.0 - math.exp(-dt / math.max(FogSmoothingSeconds,0.001))
              _FogLast = _FogLast or w['Fog'] or targetF
              local nextF = Lerp(_FogLast, targetF, alphaF)
              w['Fog'] = nextF; _FogLast = nextF
          end

          if TokyoTintEnabled and w then
            if not _AmbientFogBase then
              local base = w['Ambient Fog Color']
              if type(base) == 'table' and base[1] then
                _AmbientFogBase = {base[1], base[2] or base[1], base[3] or base[1], base[4] or 1.0}
              else
                _AmbientFogBase = {0.48,0.58,0.68,1.0}
              end
            end
            local tintFactor, warmColor, phase = _TokyoTintCompute(TOD)
            if tintFactor > 0 and warmColor then
              local b = _AmbientFogBase or {0.48,0.58,0.68,1.0}
              local tf = Clamp(tintFactor,0,1)
              local tinted = {
                b[1] + (warmColor[1]-b[1])*tf,
                b[2] + (warmColor[2]-b[2])*tf,
                b[3] + (warmColor[3]-b[3])*tf,
                1.0
              }
              pcall(function() w['Ambient Fog Color'] = tinted end)
              _TokyoTintWasActive = true
            else
              if _TokyoTintWasActive and _AmbientFogBase then
                pcall(function() w['Ambient Fog Color'] = _AmbientFogBase end)
              end
              _TokyoTintWasActive = false
            end
          end

          if _PresetActive and _PresetTargets then _apply_precip_and_wind(w, _PresetTargets) end
          if _PresetActive and _PresetTargets and ((_PresetTargets.rain or 0) <= 0.05) and not _PresetTargets.lightning then
            withUDW(function(udw) _UDWForceDry(udw) end)
          end

          -- 處理慢速過渡邏輯
          if _ResumeSlowPending and (not _ReassertLeft or _ReassertLeft <= 0) and A and A:IsValid() then
            local todNow = A['Time Of Day'] or 0
            local want, rsn = false, (SavedSlowReason or "none")
            if rsn == "dawn" then want = (todNow >= SlowWindowDawnStart and todNow <= SlowWindowDawnEnd)
            elseif rsn == "dusk" then want = (todNow >= SlowWindowDuskStart and todNow <= SlowWindowDuskEnd)
            else
              want = (todNow >= SlowWindowDawnStart and todNow <= SlowWindowDawnEnd) or (todNow >= SlowWindowDuskStart and todNow <= SlowWindowDuskEnd)
              rsn  = (todNow >= SlowWindowDawnStart and todNow <= SlowWindowDawnEnd) and "dawn" or "dusk"
            end
            if want and not _TransitionActive then
              _TransitionActive = true
              _TransitionReason = rsn
              _TransitionLastFlip = os.clock()
              pcall(function() A['Simulation Speed'] = TransitionSlowSpeed end)
              if _PrevSimSpeed and _PrevSimSpeed > 0.01 then
                local curTS3 = A['Time Speed'] or BaselineTimeSpeed
                local scaled = math.max(0.01, curTS3 * (TransitionSlowSpeed / _PrevSimSpeed))
                pcall(function() A['Time Speed'] = scaled end)
              end
            end
            _ResumeSlowPending = false
          end

          local TOD2 = (A and A:IsValid()) and (A['Time Of Day'] or 1200) or 1200
          local inDawnWindow = (TOD2 >= SlowWindowDawnStart and TOD2 <= SlowWindowDawnEnd)
          local inDuskWindow = (TOD2 >= SlowWindowDuskStart and TOD2 <= SlowWindowDuskEnd)
          local wantTransition = (inDawnWindow or inDuskWindow)
          local nowClock = os.clock()
          if not _TransitionLastFlip then _TransitionLastFlip = nowClock end
          local reason = inDawnWindow and "dawn" or (inDuskWindow and "dusk" or "none")

          if TransitionAutoEnabled and (not _ReassertLeft or _ReassertLeft <= 0) and A and A:IsValid() then
            if wantTransition then
              _TransitionHoldLeft = TransitionHoldSeconds
              if not _TransitionActive and (nowClock - _TransitionLastFlip) >= TransitionMinGapSeconds then
                _TransitionActive = true
                _TransitionReason = reason
                _TransitionLastFlip = nowClock
                pcall(function() A['Simulation Speed'] = TransitionSlowSpeed end)
                if _PrevSimSpeed and _PrevSimSpeed > 0.01 then
                  local curTS4 = A['Time Speed'] or BaselineTimeSpeed
                  local scaled = math.max(0.01, curTS4 * (TransitionSlowSpeed / _PrevSimSpeed))
                  pcall(function() A['Time Speed'] = scaled end)
                end
              end
            else
              if _TransitionActive then
                _TransitionHoldLeft = _TransitionHoldLeft - dt
                local passedEnd = false
                if _TransitionReason == "dawn" then passedEnd = (TOD2 >= (SlowWindowDawnEnd - EPS_TOD))
                elseif _TransitionReason == "dusk" then passedEnd = (TOD2 >= (SlowWindowDuskEnd - EPS_TOD)) end
                if _TransitionReason == "dusk" and TOD2 >= (SlowWindowDuskEnd + SlowSafetyRestoreMarginUDS) then passedEnd = true end
                if passedEnd and _TransitionHoldLeft <= 0 and (nowClock - _TransitionLastFlip) >= (TransitionMinGapSeconds*0.5) then
                  _TransitionActive = false
                  _TransitionLastFlip = nowClock
                  pcall(function() A['Animate Time of Day'] = true end)
                  pcall(function() A['Simulation Speed'] = SimulationSpeed end)
                  local restoreTS = (_PrevTimeSpeed ~= nil and _PrevTimeSpeed ~= 0) and _PrevTimeSpeed or BaselineTimeSpeed
                  pcall(function() A['Time Speed'] = restoreTS end)
                  _PrevSimSpeed, _PrevTimeSpeed = nil, nil
                  _TransitionReason = "none"
                end
              end
            end
          end
          
          -- 定期保存狀態
          do
            local Astate = CachedCourseSkyActor
            local curTODsnap = (Astate and Astate:IsValid()) and (Astate['Time Of Day'] or nil) or nil
            if curTODsnap then
              if not _LastStateSaveTOD then _LastStateSaveTOD = curTODsnap
              else
                local diff = (curTODsnap - _LastStateSaveTOD) % 2400
                if diff < 0 then diff = diff + 2400 end
                if diff >= 25.0 then
                  SaveStateSnapshot("interval")
                  _LastStateSaveTOD = curTODsnap
                end
              end
            end
          end
        end -- if course end
      end -- function _TickBody end
      
      local ok, err = pcall(_TickBody)
      if not ok then Log("Tick error: %s", tostring(err)) end
    end)
    return false
  end)
end)



local function _ModTime(amount)
    if not CachedCourseSkyActor or not CachedCourseSkyActor:IsValid() then return end
    _ManualControlActive = true 
    local newT = (CachedCourseSkyActor['Time Of Day'] or 0) + amount
    if newT > 2400 then newT = newT - 2400 end
    if newT < 0 then newT = newT + 2400 end
    CachedCourseSkyActor['Time Of Day'] = newT
end

local function _ModWeather(type, amount)
    local w = GetWeather()
    if not w then return end
    _ManualControlActive = true 
    if type == "cloud" then
        pcall(function() w['Cloud Coverage - Manual Override'] = true end)
        local c = (w['Cloud Coverage'] or 0) + amount
        w['Cloud Coverage'] = Clamp(c, 0, 10)
    elseif type == "fog" then
        pcall(function() w['Fog - Manual Override'] = true end)
        local f = (w['Fog'] or 0) + amount
        w['Fog'] = Clamp(f, 0, 30)
    end
end

-- ==========================================================
-- [工具] 格式化時間：將 UDS 2400 轉為標準 HH:MM (60進位修正版)
-- ==========================================================
function FormatToTime(uds_time)
    if not uds_time then return "00:00" end
    
    -- 計算小時 (0-23)
    local hours = math.floor(uds_time / 100)
    
    -- 計算分鐘 (將 0-99 的小數百分比轉為 0-59 分)
    local raw_minutes = uds_time % 100
    local minutes = math.floor((raw_minutes / 100) * 60)
    
    -- 格式化輸出，自動補零 (例如 09:05)
    return string.format("%02d:%02d", hours, minutes)
end

-- ==========================================================
-- [優化] M: 顯示時間監控 (英文 CMD 版 / 60進位修正)
-- ==========================================================
SafeRegisterKeyBind(Key.M, {}, function()
    -- 獲取系統現實時間
    local realTime = os.date("%H:%M:%S")
    
    -- 優先從全域口袋拿 TOD，如果拿不到才去抓 Actor
    local currentTOD = _G.TXR_CURRENT_TOD or (CachedCourseSkyActor and CachedCourseSkyActor['Time Of Day']) or 0
    
    if currentTOD > 0 then
        -- 調用 TXRLog (自動套用你的 6 格縮進工具)
        TXRLog("TXR TIME MONITOR", {
            "Real Time : " .. realTime,
            "Game Time : " .. FormatToTime(currentTOD),
            "Status    : ACTIVE"
        })
    else
        -- 找不到時間時的警告 (英文版避免 CMD 亂碼)
        TXRLog("TXR TIME MONITOR", {
            "Real Time : " .. realTime,
            "Game Time : SEARCHING..."
        })
    end
end)

-- [優化] Alt + 3: 天氣預報監控 (修復時間格式與實時倒數)
RegisterKeyBind(Key.THREE, { ModifierKey.ALT }, function()
    local current = _PresetName or "None (Clear)"
    local next_w  = _NextPresetName or "TBD"
    
    -- 1. 獲取當前引擎時間 (TOD)
    -- 確保 TOD 變數存在，若不存在則從 Actor 獲取
    local current_tod = TOD or 0
    if CachedCourseSkyActor and CachedCourseSkyActor:IsValid() then
        current_tod = CachedCourseSkyActor['Time Of Day'] or 0
    end

    local forecast_time = "N/A"
    local wait_mins = 0

    if _NextPresetTOD then
        -- 使用你寫好的 FormatToTime 確保不會出現 08:68 (會正確轉為 09:08)
        forecast_time = FormatToTime(_NextPresetTOD)
        
        -- 2. 計算實時倒數分鐘數
        -- 計算當前 TOD 與 目標 TOD 的差距
        local gap = (_NextPresetTOD - current_tod) % 2400
        if gap < 0 then gap = gap + 2400 end
        
        -- UDS 數值轉為現實分鐘 (UDS 100 單位 = 60 分鐘，所以乘以 0.6)
        wait_mins = math.floor(gap * 0.6)
    end

    -- 3. 輸出
    TXRLog("TXR WEATHER: MONITOR", {
        "Current Style : " .. current,
        "Next Preset   : " .. next_w,
        "Expected At   : " .. forecast_time .. " (Countdown: " .. wait_mins .. " mins)",
        "System Status : " .. (wait_mins <= 0 and "Transitioning..." or "Stable")
    })
end)

-- U: 重置所有設定 (CMD 顯示英文 / 代碼備註中文)
SafeRegisterKeyBind(Key.U, {}, function()
    -- [標記重置] 關閉所有腳本手動干預開關
    _ManualControlActive = false
    _PresetActive = false
    
    -- [狀態檢查] 檢查天色與天氣物件是否有效
    local skyValid = (CachedCourseSkyActor and CachedCourseSkyActor:IsValid())
    local weatherValid = (GetWeather() and GetWeather():IsValid())
    
    local skyStatus = skyValid and "OK" or "LOST"
    local weatherStatus = weatherValid and "OK" or "LOST"

    -- [執行恢復] 恢復時間流動與天氣自動模式
    pcall(function()
        if skyValid then
            CachedCourseSkyActor['Animate Time of Day'] = true
            CachedCourseSkyActor['Simulate Real Sun'] = true
            CachedCourseSkyActor['Simulation Speed'] = SimulationSpeed or 1.0
        end
        if weatherValid then
            local w = GetWeather()
            -- 將手動覆蓋設為 false，歸還控制權給 UDS 引擎
            w['Cloud Coverage - Manual Override'] = false
            w['Fog - Manual Override'] = false
            w['Rain - Manual Override'] = false
        end
    end)

    -- [調用工具] 使用你自定義的 6 格縮進 TXRLog 輸出英文資訊
    TXRLog("TXR SYSTEM RECOVERY", {
        "Manual Control : OFF",           -- 手動控制：已關閉
        "Time Animation : AUTO",          -- 時間動畫：恢復自動
        "Detection      : Sky[" .. skyStatus .. "] Weather[" .. weatherStatus .. "]", -- 偵測結果
        "System Status  : ALL RESET"       -- 系統狀態：全部重置
    })
end)

-- Keybinds (快捷鍵綁定)
SafeRegisterKeyBind(Key.Y, {ModifierKey.ALT}, function() _ModTime(10) end)   -- Alt+Y: 時間 +10
SafeRegisterKeyBind(Key.Y, {ModifierKey.SHIFT}, function() _ModTime(100) end) -- Shift+Y: 時間 +100
SafeRegisterKeyBind(Key.T, {ModifierKey.ALT}, function() _ModTime(-10) end)   -- Alt+T: 時間 -10
SafeRegisterKeyBind(Key.T, {ModifierKey.SHIFT}, function() _ModTime(-100) end) -- Shift+T: 時間 -100
SafeRegisterKeyBind(Key.P, {ModifierKey.ALT}, function() _ModWeather("fog", 0.5) end) -- Alt+P: 霧 +0.5
SafeRegisterKeyBind(Key.P, {ModifierKey.SHIFT}, function() _ModWeather("fog", 2.0) end) -- Shift+P: 霧 +2.0
SafeRegisterKeyBind(Key.O, {ModifierKey.ALT}, function() _ModWeather("fog", -0.5) end) -- Alt+O: 霧 -0.5
SafeRegisterKeyBind(Key.O, {ModifierKey.SHIFT}, function() _ModWeather("fog", -2.0) end) -- Shift+O: 霧 -2.0
SafeRegisterKeyBind(Key.L, {ModifierKey.ALT}, function() _ModWeather("cloud", 0.5) end) -- Alt+L: 雲 +0.5
SafeRegisterKeyBind(Key.L, {ModifierKey.SHIFT}, function() _ModWeather("cloud", 2.0) end) -- Shift+L: 雲 +2.0
SafeRegisterKeyBind(Key.K, {ModifierKey.ALT}, function() _ModWeather("cloud", -0.5) end) -- Alt+K: 雲 -0.5
SafeRegisterKeyBind(Key.K, {ModifierKey.SHIFT}, function() _ModWeather("cloud", -2.0) end) -- Shift+K: 雲 -2.0
-- EOF (檔案結束)

-- ============================================================
-- [TXR ENHANCED TIME CONTROLS] - 精確時間版
-- ============================================================

-- 1. 自定義跳轉時間 (HH:MM 格式)
local TXR_DAWN = 730.0   -- 07:30 (早晨)
local TXR_DUSK = 1730.0  -- 17:30 (黃昏)

-- 2. 通用跳轉執行函數 (穩定無紅字版)
local function txr_fast_snap(target_val)
    ExecuteInGameThread(function()
        -- 確保物件有效性
        local Sky = CachedCourseSkyActor or A
        if not Sky or not Sky:IsValid() then return end
        
        -- 確保關閉系統時間同步
        if Sky['Use System Time'] then
            Sky['Use System Time'] = false
        end

        -- 暫存播放狀態
        local currentAnim = Sky['Animate Time of Day']
        
        -- 核心跳轉：直接修改屬性，絕對不調用 SnapToTime
        Sky['Time Of Day'] = target_val
        
        -- 補償機制：切換一次動畫開關，強制 UDS 引擎刷新光影
        Sky['Animate Time of Day'] = not currentAnim
        Sky['Animate Time of Day'] = currentAnim
        
        -- 顯示 TXRLog 盒子日誌
        TXRLog("TXR TIME SNAP", {
            "Target Time : " .. FormatToTime(target_val),
            "Status      : SNAP SUCCESS"
        })
    end)
end

-- 3. [Alt + F] 時間凍結開關
RegisterKeyBind(Key.F, { ModifierKey.ALT }, function()
    ExecuteInGameThread(function()
        local Sky = CachedCourseSkyActor or A
        if not Sky or not Sky:IsValid() then return end
        
        local isPlaying = Sky['Animate Time of Day']
        local newState = not isPlaying
        Sky['Animate Time of Day'] = newState
        
        TXRLog("TXR TIME FREEZE", {
            "State  : " .. (newState and "FLOWING (ON)" or "FROZEN (OFF)"),
            "Action : " .. (newState and "Time animation enabled" or "Time animation stopped")
        })
    end)
end)

-- 4. [Alt + D / N] 時間跳轉註冊
-- Alt + D 跳轉至 07:30
RegisterKeyBind(Key.D, { ModifierKey.ALT }, function() txr_fast_snap(TXR_DAWN) end)
-- Alt + N 跳轉至 17:30
RegisterKeyBind(Key.N, { ModifierKey.ALT }, function() txr_fast_snap(TXR_DUSK) end)

